# Module Documentation in README - using _doc_to_readme_

Use external pipeline and doc_to_md.py from [GitHub Repo `doc_to_readme`](https://github.com/ziselsberger/doc_to_readme), 
to add a table of all python functions & classes at the end of this README file.

### GitLab
1. Have a look at the [GitLab Instructions](https://github.com/ziselsberger/doc_to_readme/blob/main/README.md#gitlab) 
on how to:
* set up a **Project Access Token** 
* add the token to the **CI Variables**

##### 2. Create [GitLab Pipeline](.gitlab-ci.yml)
There are two options

a) use `include` / `include:remote` --> Pipeline YAML file stored in GitHub
```yaml
include: "https://github.com/ziselsberger/doc_to_readme/raw/main/templates/.update_readme_gitlab.yml"
```

b) use `include:project` --> only possible if you have access to this [GitLab project](https://git.uibk.ac.at/csat6025/doc_to_readme)
```yaml
include:
  project: 'csat6025/doc_to_readme'
  file: '/templates/.update_readme_gitlab.yml'
```


## Functions & Classes  
| Module | Type | Name/Call | Description |
| --- | --- | --- | --- |
| [main](./execute_doc_to_readme/main.py) | function  | `hello_world()` | Just says hello |

---
**Last Update:** 2023-04-18