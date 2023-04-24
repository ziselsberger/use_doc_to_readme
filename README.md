# _doc_to_readme_ - Automated Module Documentation

### Step-by-step intro on how to integrate [doc_to_readme](https://github.com/ziselsberger/doc_to_readme) in your repository

> * [GitHub](#github)
> * [GitLab](#gitlab)
> * [Bitbucket](#bitbucket)

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
      EXCLUDED_MODULES: "doc_to_md"
      SELECTED_MODULES: ""
      SEPARATED: "true"
```

#### 3. Update branch name and variables (if needed)

* Branch name defaults to **main**
* These _**variables**_ are passed to the [reusable workflow](https://github.com/ziselsberger/doc_to_readme/blob/main/.github/workflows/update_readme_github.yml) as _**inputs**_ for [doc_to_md.py](https://github.com/ziselsberger/doc_to_readme/blob/main/src/doc_to_md.py).: 
  * `PATH_TO_README` 
  * `EXCLUDED_MODULES` 
  * `SELECTED_MODULES` 
  * `SEPARATED`

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
  SEPARATED:
    required: false
    default: "true"  # create one table per module
    type: string
```
---

> #### More information about reusable workflows:  
> https://dev.to/n3wt0n/avoid-duplication-github-actions-reusable-workflows-3ae8  
> https://docs.github.com/en/actions/using-workflows/reusing-workflows#creating-a-reusable-workflow

---

## GitLab
#### 1. Have a look at these [Instructions](https://github.com/ziselsberger/doc_to_readme/blob/main/How_to_setup_the_pipelines.md#gitlab) on how to:
* set up a **Project Access Token** 
* add the token to the **CI Variables**

#### 2. Use this [GitLab CI File (.yml)](.gitlab-ci.yml)

The environment variables of the external pipeline file can be overwritten in .gitlab-ci.yml file:

```yaml
variables:
  PATH_TO_README: "README.md"
  EXCLUDED_MODULES: "doc_to_md"
  SELECTED_MODULES: ""
  SEPARATED: "true"
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

> #### GitLab Documentation:   
> * https://docs.gitlab.com/ee/ci/yaml/includes.html  
> * https://docs.gitlab.cn/14.0/ee/ci/yaml/includes.html#overriding-external-template-values  
---

## Bitbucket

#### 1. Have a look at these [Instructions](https://github.com/ziselsberger/doc_to_readme/blob/main/How_to_setup_the_pipelines.md#bitbucket) on how to:
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
              git push
            fi 
          script:
            - *prep
            - python3 doc_to_md.py -f README.md [-e EXCLUDED_MODULES] [-m SELECTED_MODULES] [--separated]
            - *push
```

#### 3. Check & update (if needed)

* BRANCH_NAME (default is **main**):  
  ```yaml
  pipelines:
    branches:
      BRANCH_NAME:
        ... 
  ```
  
* PATH_TO_README
  ```yaml
  # .push:
  git add PATH_TO_README   
  
  # script:
  python3 doc_to_md.py -f PATH_TO_README  
  ```
  
* Call `doc_to_md.py` with optional arguments (-e / -m / --separated)  
  ```shell
  -e EXCLUDED_MODULES # excluded module name(s) (without '.py'), separated with a whitespace, e.g. "module_x module_y"
  -m SELECTED_MODULES # selected module(s)
  --separted  # create one table per module
  ```

---
> **Currently, it's not possible to use pipeline yml files from other repositories.**  
> Status on ongoing development: https://jira.atlassian.com/browse/BCLOUD-14078
---

## Functions & Classes  
### [main](./main.py)

| Type | Name/Call | Description |
| --- | --- | --- |
| function  | `hello_world()` | Just says hello |
### [my_functions](./src/my_functions.py)

| Type | Name/Call | Description |
| --- | --- | --- |
| function  | `mean(x: int = 1, y: int = 2) -> float` | Calculate mean of x and y. |
| function  | `add(x: int = 4, y: int = 5) -> int` | Add two numbers (x and y). |
| function  | `multiply(x: int = 6, y: int = 7) -> int` | Multiply two numbers (x and y). |
### [my_classes](./src/my_classes.py)

| Type | Name/Call | Description |
| --- | --- | --- |
| class  | `TechnicalQualityTests` | Base class for all technical QC Tests. |
| method (TechnicalQualityTests) | `add_to_dict(self, test_name: str, test_result: Tuple[bool, str]) -> None` | Add QC result to dictionary. |

Created with: [doc_to_readme](https://github.com/ziselsberger/doc_to_readme)  
[MIT](https://github.com/ziselsberger/doc_to_readme/blob/main/LICENSE) &copy; 2023 Mirjam Ziselsberger

---
**Last Update:** 2023-04-24