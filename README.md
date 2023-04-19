# Module Documentation - using _doc_to_readme_

Use pipeline and doc_to_md.py from [GitHub Repo `doc_to_readme`](https://github.com/ziselsberger/doc_to_readme), 
to add a table of all python functions & classes at the end of this README file.

### GitHub

##### 1. Add dir `.github/workflows`

##### 2. Create [Workflow file (.yml)](.github/workflows/update_readme.yml)

> GitHub **reusable workflows**:  
> https://dev.to/n3wt0n/avoid-duplication-github-actions-reusable-workflows-3ae8  
> https://docs.github.com/en/actions/using-workflows/reusing-workflows#creating-a-reusable-workflow

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

`PATH_TO_README`, `EXCLUDED_MODULES` & `SELECTED_MODULES` are passed to the [reusable workflow](https://github.com/ziselsberger/doc_to_readme/blob/main/.github/workflows/update_readme_github.yml) as inputs for doc_to_md.py

```yaml
inputs:
  PATH_TO_README:
    required: false
    default: "README.md"
    type: string
  EXCLUDED_MODULES:
    required: false
    type: string
  SELECTED_MODULES:
    required: false
    type: string
```

---

### GitLab
##### 1. Have a look at these [Instructions](https://github.com/ziselsberger/doc_to_readme/blob/main/README.md#gitlab) on how to:
* set up a **Project Access Token** 
* add the token to the **CI Variables**

##### 2. Create [GitLab Pipeline](.gitlab-ci.yml)
There are two options

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

GitLab Documentation: https://docs.gitlab.com/ee/ci/yaml/includes.html

---

### Bitbucket
> Currently not possible to use pipeline yml files from other repositories.  
> Ongoing development: https://jira.atlassian.com/browse/BCLOUD-14078

##### 1. Have a look at these [Instructions](https://github.com/ziselsberger/doc_to_readme/blob/main/README.md#bitbucket) on how to:
* enable pipelines 

##### 2. Use this [YAML](bitbucket-pipelines.yml) as Template

```yaml
pipelines:
  branches:
    main:
      - step:
          name: doc_to_readme
          .push: &push |
            lines=$(git status -s | wc -l)
            if [ $lines -gt 0 ];then
              git add "README.md"
              git commit -m "Auto-update README.md [skip ci]"
              git push main
            fi 
          script:
            - ...
            - python3 doc_to_md.py -f README.md
            - ...
```

##### 3. Check & update if needed

* Branch name (default is **main**):  

  > **.push:**   
  >  git push _main_
  
* Path to README
  > **.push:** git add _"README.md"_  
  > **script:** python3 doc_to_md.py -f _README.md_

---

## Functions & Classes  
| Module | Type | Name/Call | Description |
| --- | --- | --- | --- |

Created with: [doc_to_readme](https://github.com/ziselsberger/doc_to_readme)  

---
**Last Update:** 2023-04-19