---
id: ee698ea8-0a9e-496c-9d0d-4c6551d4223a
title: ia test
desc: ''
created: 1747413678
updated: 1747413678
author: Sergei Kryazvevskikh <soliverr@gmail.com>
tags:  ia,test
---
# ia test

The Moon is estimated to be around 4.5 billion years old, roughly the same age as the Earth.



<hr class="__chatgpt_plugin">

### role::assistant<span style="font-size: small;"> (gpt-4o-mini)</span>

To log effective SQL in Dagster when running a dbt model, you can follow these steps:

1. **Setup a Dagster Repository**: If you haven't already set up a Dagster repository, create one which will contain your dbt models and their associated jobs.

2. **Define a Dagster Job**: In your Dagster job, you will need to define a solid (or op in the newer versions of Dagster) that calls your dbt model run. Within that op, you can use the dbt's `run` command to execute your models.

3. **Capture SQL Logs**: To log the SQL being executed by dbt, you can utilize dbt's built-in logging capabilities along with Python's logging module within your Dagster op.

4. **Example Implementation**:
   Here’s an example implementation of how you can achieve this:

```python
import logging
from dagster import job, op
import subprocess
import dbt.main as dbt_main

# Configure logging
logging.basicConfig(level=logging.INFO)
logger = logging.getLogger(__name__)

@op
def run_dbt_model(context):
    # Specify your target dbt model
    dbt_model = "your_dbt_model"  # replace with your actual model
    dbt_project_dir = "/path/to/your/dbt/project"  # replace with actual path
    
    try:
        # Run the dbt model
        context.log.info(f"Running

<hr class="__chatgpt_plugin">

### role::user


```I use Dagster and dbt together. I want to log into Dagster's log an effective sql that produce dbt when running model. How can I to achieve this?
