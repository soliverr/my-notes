---
id: zxcwueods8ztwzip0yk3ta2
title: Datawarehouse
desc: ''
updated: 1687960317281
created: 1676629988555
---

[Data warehouse](https://en.wikipedia.org/wiki/Data_warehouse)

# Concepts

* https://www.astera.com/type/blog/data-warehouse-concepts/
* https://www.jamesserra.com/archive/2012/03/data-warehouse-architecture-kimball-and-inmon-methodologies/


# Data Vault

* [Data vault modeling](https://en.wikipedia.org/wiki/Data_vault_modeling)
* [Building a Scalable Data Warehouse with Data Vault 2.0 1st Edition by Daniel Linstedt (Author), Michael Olschimke (Author)](https://vdoc.pub/documents/building-a-scalable-data-warehouse-with-data-vault-20-1j3bvejdktdodb)
* https://dbtvault.readthedocs.io/en/latest/tutorial
* https://docs.varigence.com/bimlflex/delivering-data-vault/data-vault-introduction
* https://www.scalefree.com/scalefree-newsletter/

## Modeling Basics

* [Data Vault 2.0 Modeling Basics](https://www.vertabelo.com/blog/data-vault-series-data-vault-2-0-modeling-basics/)
* https://www.vertabelo.com/blog/data-vault-series-building-an-information-mart-with-your-data-vault/

The DV is a source of FACTS, not a source of TRUTH (truth is often subjective & relative in the data world).

![](/assets/images/2023-05-12-15-58-10.png)

Just remember this list:

* Hubs = Business Keys
* Links = Associations / Transactions
* Satellites = Descriptors

### Hubs

Hubs must be source system agnostic. That means they must be based on true Business Keys (or meaningful natural keys) that are not tied to any one source system. That means you should not use source system surrogate keys for identification. _Hubs keys must be based on an identifiable business element or elements_. This is the most important aspect of Data Vault modeling. 

Fundamentally, a Hub is a list of unique business keys.

A Hub table has a very simple structure. It contains:

* A Hub PK
* The Business Key column(s)
* The Load Date (LOAD_DTS)
* The Source for the record (REC_SRC)

New in DV 2.0, the **Hub PK** is a calculated field consisting of a Hash (often MD5) of the Business Key columns.

The **Business Key** must be a declared unique or alternate key constraint in the Hub. That means for each key there will be only on row in the Hub table, ever. It can be a compound key made up of more than one column.

The **LOAD_DTS** tells us the first time the data warehouse “knew” about that business key. So no matter how many loads you run, this row is created the first time and never dropped or updated.

The **REC_SRC** tells us which source system the row came from. If the value can come from multiple sources, this will tell us which source fed us the value first.

_Example:_

![](/assets/images/2023-05-12-15-55-35.png)

In the HUB_LOCATION table, LOCATION_NAME is the Business Key. It is a unique name used in the source systems to identify a location. It must have a unique constraint or index declared on it in the database to prevent duplicates from being entered and to facilitate faster query access.

#### What can you do with a single Hub table?

You can do data profiling and basic analysis on the business key. Answer questions like:

* How many locations do we have?
* How many source systems provide us locations names?
* Are there data quality issues? Do we see Location Names that seem similar but are from different sources? (Hint: if you do, you may have a master data management issue)

#### Hash-based Primary Keys

One of the innovations in DV 2.0 was the replacement of the standard integer surrogate keys with hash-based primary keys. This was to allow a DV solution to be deployed, at least in part, on a Hadoop solution. Hadoop systems do not have surrogate key generators like a modern RDBMS, but you can generate an MD5 Hash. With a Hash Key in Hadoop and one in your RDBMS, you can “logically” join this data (with the right tools of course). (Plus sequence generators and counters can become a bottleneck on some systems at very high volumes and degrees of parallelism.)

### Links

The Link is the key to flexibility and scalability in the Data Vault modeling technique. They are modeled in such a way as to allow for changes and additions to the model over time by providing the ability to easily add new objects and relationships without having to change existing structures or load routines.

In a Data Vault model, all source data relationships (i.e., foreign keys) and events are represented as Links. One of the foundational rules in DV is that Hubs can have no FKs, so to represent the joins between Hub concepts, we must use a Link table. The purpose of the Link is to capture and record the relationship of data elements at the lowest possible grain. Other examples of Links include transactions and hierarchies (because in reality those are the intersection of a bunch of Hubs too).

A Link is therefore an intersection of business keys. It contains the columns that represent the business keys from the related Hubs. A Link must have more than one parent table. There must be at least two Hubs, but, as in the case of a transaction, they may be composed of many Hubs. A Link table’s grain is defined by the number of parent keys it contains (very similar to a Fact table in dimensional modeling).

Like a Hub, the Link is also technically a simple structure. It contains:

* A Link PK (Hash Key)
* The PKs from the parent Hubs – used for lookups
* The Business Key column(s) – new feature in DV 2.0
* The Load Date (LOAD_DTS)
* The Source for the record (REC_SRC)

![](/assets/images/2023-05-12-15-56-58.png)

In Figure 3 you see the results of converting the FK to REGIONS from the COUNTRIES table (Figure 1) into a Link table. The PK column LNK_REGION_COUNTRY_KEY is a hash key calculated against the Business Key columns from the contributing Hubs. This gives us a unique key for every combination of Country and Region that may be fed to us by the source systems.

Just as in the Hubs, the Link records only the first time that the relationship appears in the DV.

In addition, the Link contains the PKs from the parent Hubs (which should be declared as an alternate unique key or index). This makes up the natural key for the Link.

New to DV 2.0 is the inclusion of the text business key columns from the parent Hubs. Yes, this is a specific de-normalization. Why? For query performance when you want to extract data from the Data Vault (more on this in part 3 & 4 of this series). Depending on your platform, you may consider adding a unique key constraint or index on these columns as well.

### Satellites

Satellites (or Sats for short) are where all the big action is in a Data Vault. These structures are where all the descriptive (i.e., non-key) columns go, plus this is where the Change Data Capture (CDC) is done and history is stored. The structure and concept are very much like a Type 2 Slowly Changing Dimension.

To accomplish this function, the Primary Key for a Sat contains two parts: the PK from its Parent Hub (or Link) plus the LOAD_DTS. So every time we load the DV and find new records or changed records, we insert those records into the Sats and give them a timestamp. (On a side note, this structure also means that a DV is real-time-ready in that you can load whenever and as often as you need as long as you set the LOAD_DTS correctly.)

This is the only structure in the core Data Vault that has a two-part key. That is as complicated as it gets from a structure perspective.

Of course, a Sat must also have the REC_SRC column for auditability. REC_SRC will tell us the source of each row of data in the Sat. The REC_SRC in a Sat does NOT have to be the same as that in the parent Hub or Link. 

![](/assets/images/2023-05-12-16-05-14.png)

In Figure 4, look at SAT_LOCATIONS. There you see all the address columns from the original table LOCATIONS (Figure 1). Likewise in SAT_COUNTRIES you see COUNTRY_NAME and in SAT_REGIONS you see REGION_ID (_remember that was the source system PK but NOT the Business Key, so it goes here so we can trace back to the source if needed_).

#### Use of HASH_DIFF columns for Change Data Capture (CDC)

Another innovation that came with DV 2.0 is the use of a Hash-based column for determining if a record in the source has changed from what was previously loaded into the Data Vault. We call that a Hash Diff (for difference). Every Sat must have this column to be DV 2.0 compliant.

So, how this works is that you first calculate a Hash on the combination of all of the descriptive (non-meta data) columns in the Sat.

Examples for Oracle for the three Sats in Figure 4 are:

```
SAT_REGIONS.HASH_DIFF = dbms_obfuscation_toolkit.md5(TO_CHAR(region_id))

SAT_COUNTRIES.HASH_DIFF = dbms_obfuscation_toolkit.md5(TRIM(country_name))

SAT_LOCATIONS.HASH_DIFF =
   dbms_obfuscation_toolkit.md5(
      TRIM(street_address) || ’^’ || TRIM(city) || ^ || TRIM(state_province) 
      || ’^’ || TRIM(postal_code))
```

Now when you get ready to do another load, you must calculate a Hash on the inbound values (using exactly the same formula) and then compare that to the HASH_DIFF column in the Sat for the most recent row (i.e., from the last load date) for the same Hub Business Key. If the Hash calculation is different, then you create a new row. If not, you do nothing (which is way faster).

**There are no updates! We never over write data in a Data Vault! We only insert new data. That allows us to keep a clean audit trail.**

### Bridge and Point-in-Time tables


https://www.data-vault.co.uk/dbtvault-pits-and-bridges/

> Although they have similar structures in some ways, they achieve different things. The PITs identify valid data on specific dates, Bridges identify valid relationships on specific dates.

# Anchor Modleing

* https://www.anchormodeling.com/tutorials/
* https://habr.com/ru/companies/avito/articles/322510/ 