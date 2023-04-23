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
      EXCLUDED_MODULES: ""
      SELECTED_MODULES: ""
```

#### 3. Update branch name and variables (if needed)

* Branch name defaults to **main**
* These _**variables**_ are passed to the [reusable workflow](https://github.com/ziselsberger/doc_to_readme/blob/main/.github/workflows/update_readme_github.yml) as _**inputs**_ for [doc_to_md.py](https://github.com/ziselsberger/doc_to_readme/blob/main/src/doc_to_md.py).: 
  * `PATH_TO_README` 
  * `EXCLUDED_MODULES` 
  * `SELECTED_MODULES` 

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
            - python3 doc_to_md.py -f README.md [-e EXCLUDED_MODULES] [-m SELECTED_MODULES]
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
  
* Call `doc_to_md.py` with optional arguments (-e / -m)  
  ```shell
  -e EXCLUDED_MODULES # excluded module name(s) (without '.py'), separated with a whitespace, e.g. "module_x module_y"
  -m SELECTED_MODULES # selected module(s)
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
### [doc_to_md](./doc_to_md.py)

| Type | Name/Call | Description |
| --- | --- | --- |
| function  | `loop_through_repo(file: str, root_dir: str = None, exclude_modules: Tuple[str, ...] = (), specified_modules: Optional[Tuple[str, ...]] = None) -> None` | Collect documentation from functions & classes |
| function  | `add_summary_to_md(overview_dict: Dict[str, Optional[Union[str, Dict[str, str]]]], markdown: str, separate: bool = True)` | Add Table with all Functions & Classes to Markdown file. |
| function  | `update_markdown_file(file: str = "../README.md", root_dir: str = None, exclude_modules: Tuple[str, ...] = (), specified_modules: Optional[Tuple[str, ...]] = None, separate: bool = True)` | Add/update 'Functions & Classes' Section in Markdown file. |
| function  | `parse_through_file(file: str) -> Dict[str, Dict[str, str]]` | Parse through module and gather info on classes and functions |

Created with: [doc_to_readme](https://github.com/ziselsberger/doc_to_readme)  
[MIT](https://github.com/ziselsberger/doc_to_readme/blob/main/LICENSE) &copy; 2023 Mirjam Ziselsberger

---
**Last Update:** 2023-04-23