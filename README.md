# Automated Module Documentation - using _doc_to_readme_

Use pipeline and doc_to_md.py from [GitHub Repo `doc_to_readme`](https://github.com/ziselsberger/doc_to_readme), 
to add a table of all python functions & classes at the end of this README file.

## GitHub

#### 1. Add dir `.github/workflows`

#### 2. Use this [GitHub Actions Workflow File (.yml)](.github/workflows/update_readme.yml)

```yaml
name: Update README.md

on:
  push:
    branches:
      - main

jobs:
  update-docu:
    permissions:
      contents: write
    uses: ziselsberger/doc_to_readme/.github/workflows/update_readme_github.yml@main
    with:
      PATH_TO_README: "README.md"
      EXCLUDED_MODULES: ""
      SELECTED_MODULES: ""
```

`PATH_TO_README`, `EXCLUDED_MODULES` & `SELECTED_MODULES` are passed to the [reusable workflow](https://github.com/ziselsberger/doc_to_readme/blob/main/.github/workflows/update_readme_github.yml) as inputs for [doc_to_md.py](https://github.com/ziselsberger/doc_to_readme/blob/main/src/doc_to_md.py).

```yaml
inputs:
  PATH_TO_README:
    required: false
    default: "README.md"
    type: string
  EXCLUDED_MODULES:
    required: false
    type: string     # provide module name(s) (without '.py'), separated with a whitespace, e.g. "module_x module_y"
  SELECTED_MODULES:
    required: false
    type: string     # same as excluded modules
```

---

## GitLab
#### 1. Have a look at these [Instructions](https://github.com/ziselsberger/doc_to_readme/blob/main/README.md#gitlab) on how to:
* set up a **Project Access Token** 
* add the token to the **CI Variables**

#### 2. Use this [GitLab CI File (.yml)](.gitlab-ci.yml)

The environment variables of the external pipeline file can be overwritten in .gitlab-ci.yml file:

```yaml
variables:
  PATH_TO_README: "README.md"
  EXCLUDED_MODULES: ""
  SELECTED_MODULES: ""
```

There are two options, how to include the external pipeline:

a) use `include` / `include:remote` --> Pipeline YAML file stored in GitHub  

```yaml
include: "https://github.com/ziselsberger/doc_to_readme/raw/main/templates/.update_readme_gitlab.yml"
```

> It must be the URL to the **raw** YAML file, otherwise the pipeline will fail!  
> Error Message: _Included file `https://../.update_readme_gitlab.yml` does not have valid YAML syntax!_


b) use `include:project` --> only possible if you have access to this [GitLab project](https://git.uibk.ac.at/csat6025/doc_to_readme)
```yaml
include:
  project: 'csat6025/doc_to_readme'
  file: '/templates/.update_readme_gitlab.yml'
```
---

> ### GitLab Documentation:   
> * https://docs.gitlab.com/ee/ci/yaml/includes.html  
> * https://docs.gitlab.cn/14.0/ee/ci/yaml/includes.html#overriding-external-template-values  
---

## Bitbucket

#### 1. Have a look at these [Instructions](https://github.com/ziselsberger/doc_to_readme/blob/main/README.md#bitbucket) on how to:
* enable pipelines 

#### 2. Use this [Bitbucket Pipelines File](bitbucket-pipelines.yml)

```yaml
pipelines:
  branches:
    main:
      - step:
          name: doc_to_readme
          .prep: &prep |
            ...
          .push: &push |
            lines=$(git status -s | wc -l)
            if [ $lines -gt 0 ];then
              git add "README.md"
              git commit -m "Auto-update README.md [skip ci]"
              git push main
            fi 
          script:
            - *prep
            - python3 doc_to_md.py -f README.md [-e EXCLUDED_MODULES] [-m SELECTED_MODULES]
            - *push
```

#### 3. Check & update (if needed)

* Branch name (default is **main**):  

  > **.push:**   
  >  git push _main_
  
* Path to README
  > **.push:** git add _"README.md"_  
  > **script:** python3 doc_to_md.py -f _README.md_

  * Call `doc_to_md.py` with optional arguments (-e / -m)  
  ```
  -e EXCLUDED_MODULES # excluded module name(s) (without '.py'), separated with a whitespace, e.g. "module_x module_y"
  -m SELECTED_MODULES # selected module(s)
  ```

---

## Functions & Classes  
| Module | Type | Name/Call | Description |
| --- | --- | --- | --- |

Created with: [doc_to_readme](https://github.com/ziselsberger/doc_to_readme)  

---
**Last Update:** 2023-04-19