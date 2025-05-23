---
id: uxgdgz7230zf0j13xfvijux
title: GitLab
desc: ''
updated: 1686040560711
created: 1630578184367
---

# GitLab and Ansible

* https://der-jd.de/blog/2021/01/02/Using-ansible-with-gitlab-ci/
* https://medium.com/sopra-steria-norge/
* https://about.gitlab.com/blog/2019/07/01/using-ansible-and-gitlab-as-infrastructure-for-code/
* https://sq4ind.eu/testing-ansible-playbooks-using-gitlab-ci-and-molecule/
* https://kruyt.org/ansible-ci-with-gitlab/

# Права доступа в CI/CD

## GitLab Access Token overview

https://docs.gitlab.com/ee/security/token_overview.html

### Personal access tokens

https://docs.gitlab.com/ee/user/profile/personal_access_tokens.html


### Project access tokens

https://docs.gitlab.com/ee/user/project/settings/project_access_tokens.html#project-access-tokens

Для токена проекта создаётся служебный пользователь _bot_, который не требует лицензирования.

[Bot users for projects](https://docs.gitlab.com/ee/user/project/settings/project_access_tokens.html#bot-users-for-projects)

* The name is set to the name of the token.
* The username is set to `project_{project_id}_bot` for the first access token. For example, `project_123_bot`.
* The email is set to `project{project_id}_bot@noreply.{Gitlab.config.gitlab.host}`. For example, `project123_bot@noreply.example.com`.
* For additional access tokens in the same project, the username is set to `project_{project_id}_bot{bot_count}`. For example, `project_123_bot1`.
* For additional access tokens in the same project, the email is set to `project{project_id}_bot{bot_count}@noreply.{Gitlab.config.gitlab.host}`. For example, `project123_bot1@noreply.example.com`.

### Group access tokens

https://docs.gitlab.com/ee/user/group/settings/group_access_tokens.html#group-access-tokens

Each time you create a group access token, a bot user is created and added to the group. These bot users are similar to bot users for projects, except they are added to groups instead of projects. Bot users for groups:

* Do not count as licensed seats.
* Can have a maximum role of Owner for a group.

## GitLab Deploy tokens

[Deploy Token](https://docs.gitlab.com/ee/user/project/deploy_tokens/)

## Use statically-defined credentials

Для доступа к образам других проектов, когда требуется их использовать как базовый образ GitLab Runner.

Нужна переменная DOCKER_AUTH_CONFIG

* https://docs.gitlab.com/ee/ci/docker/using_docker_images.html#determine-your-docker_auth_config-data
* [Change visibility of the Container Registry](https://docs.gitlab.com/ee/user/packages/container_registry/)

# Использование Docker и Podman

[Building Docker images on GitLab CI: Docker-in-Docker and Podman](https://pythonspeed.com/articles/gitlab-build-docker-image/)

```sh
$ echo "$GITLAB_WNF3_REGISTRY_READ_TOKEN" | podman login registry.gitlab.com --username "sergej.krjazhevskih@winfinity.live" --password-stdin
$ podman run registry.gitlab.com/wnf3/data-engineering/infrastructure/base-images/ansible-bender-build
| hello |
...
```

Вот такой Dockerfile для сборки образа с `podman` и `ansible-bender`: 

```
FROM quay.io/podman/stable

RUN dnf install -y buildah \
    ansible \
    ansible-bender \
    ansible-collection-ansible-posix \
    ansible-collection-community-general \
    && dnf clean all

RUN ln -s /usr/bin/podman /usr/bin/docker

CMD ["ansible-bender", "--help"]
```

Вот пример работающего GtiLab CI/CD:

```
image: quay.io/podman/stable

before_script:
  - >
    if [ ! -e /usr/bin/docker ] ; then 
      ln -s /usr/bin/podman /usr/bin/docker
    fi
  - echo "${CI_REGISTRY_PASSWORD}" | docker login ${CI_REGISTRY} -u ${CI_REGISTRY_USER} --password-stdin

stages:
  - build
  - test

build-bender:
  stage: build
  script:
    - docker build --tag $CI_REGISTRY_IMAGE .
    - docker push $CI_REGISTRY_IMAGE
  only:
    changes:
      - Dockerfile
      - .gitlab-ci.yml

test-bender:
  image: $CI_REGISTRY_IMAGE
  stage: test
  script:
    - ansible-bender build ./simple-playbook.yaml
    - docker image ls | grep bender
    - ansible-bender list-builds
    - ansible-bender push $CI_REGISTRY_IMAGE/ansible-bender-test-image
  only:
    changes:
      - Dockerfile
      - simple-playbook.yaml
      - .gitlab-ci.yml
```

## Authenticate

* https://docs.gitlab.com/ee/ci/docker/authenticate_registry.html

### DOCKER_AUTH_CONFIG

* https://docs.gitlab.com/ee/ci/docker/using_docker_images.html#determine-your-docker_auth_config-data

```sh
printf "my_username:my_password" | openssl base64 -A
printf "sergej.krjazhevskih@winfinity.live:<access_token>" | openssl base64 -A
```

Вместо пароля нужно использовать Access Token.

Create a CI/CD variable DOCKER_AUTH_CONFIG with the content of the Docker configuration file as the value:

```json
{
    "auths": {
        "registry.example.com:5000": {
            "auth": "bXlfdXNlcm5hbWU6bXlfcGFzc3dvcmQ="
        },
        "registry.gitlab.com": {
          "auth": "c2VyZ2VqLmtyamF6aGV2c2tpaEB3aW5maW5pdHkubGl2ZTpnbHBhdC1xZ2hHUTZxWkdxeVRlUXp5MkJpOQ=="
        }
    }
}
```

# Slack Integration

* https://docs.gitlab.com/ee/user/project/integrations/gitlab_slack_application.html
* https://docs.gitlab.com/ee/user/project/integrations/slack.html

# Pipelines

## Parent-Child pipelines

* [Downstream pipelines | GitLab](https://docs.gitlab.com/ee/ci/pipelines/downstream_pipelines.html#parent-child-pipelines)
* [Trigger](https://docs.gitlab.com/ee/ci/yaml/#trigger)
* [variables - can we use dynamic jobs names in gitlab-ci.yml? - Stack Overflow](https://stackoverflow.com/questions/52260381/can-we-use-dynamic-jobs-names-in-gitlab-ci-yml)
* [Upstream triggering in Gitlab-CI aka pipeline dependencies - Stack Overflow](https://stackoverflow.com/questions/60505357/upstream-triggering-in-gitlab-ci-aka-pipeline-dependencies)

## Jobs 

* [How we used parallel CI/CD jobs to increase our productivity | GitLab](https://about.gitlab.com/blog/2021/01/20/using-run-parallel-jobs/)
* [How to trigger multiple pipelines using GitLab CI/CD | GitLab](https://about.gitlab.com/blog/2019/07/24/cross-project-pipeline/)

 # Code suggestions

 * https://docs.gitlab.com/ee/user/project/repository/code_suggestions.html