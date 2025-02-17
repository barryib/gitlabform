## Changelog

### 2.2.0

* **Add LDAP Group Links support** (**GitLab Premium (paid) only**). Implements [#140](https://github.com/egnyte/gitlabform/issues/140).

* **Add project and group badges support**. Implements [#59](https://github.com/egnyte/gitlabform/issues/59).

* Allow 0 (no access) in Protected Tags. Fixes [#172](https://github.com/egnyte/gitlabform/issues/172).

* Exit on configuration missing `projects_and_groups` key. This will provide a helpful error message for typos made in this key. Fixes [#242](https://github.com/egnyte/gitlabform/issues/242).
* Make error messages more friendly when there is no network connection or when configuration is invalid (f.e. YAML parsing errors).
* Make the output of some processors a bit more consistent.

* Fix detecting an "empty effective config" and improve the UI related to processing groups and projects with such. Fixes [#251](https://github.com/egnyte/gitlabform/issues/251).

* Big refactoring that should make adding new features easier and faster. The main change is introducing a new way to implement "processors" - thanks to a generalized `MultipleEntitiesProcessor` class adding a new feature like Project Badges should is now as easy as implementing a class like `BadgesProcessor` and writing an acceptance test like `TestBadges`. Note that this new design may change in the near future and we are open to discussions and PRs to make it even better! We also plan to create a similar generalized `SingleEntityProcessor` class soon.

* Change the User Agent that the app uses when making requests to GitLab to a custom `GitLabForm/<gitlabform_version> (python-requests/<requests_version>)`.

### 2.1.2

* Managing project members is not incredibly slow anymore. Fixes [#240](https://github.com/egnyte/gitlabform/issues/240)

Thanks to [@andrewjw](https://github.com/andrewjw) (Ocado Technology) for his contribution!

### 2.1.1

* Fixed sharing group with a subgroup. Fixes [#236](https://github.com/egnyte/gitlabform/issues/236)
* Improved re-protecting branches after updating files in them. Fail fast if the config is invalid.
* Better Docker images:
  * Updated Alpine from 3.12 to 3.14,
  * Started to build images in ARM64 architecture (apart from x86-64),
  * Started to add tags <major_version>, <major_version>.<minor_version>. Note that Alpine-based image is the main one which gets these tags. For Debian-based images add "-buster" suffix. Implements [#173](https://github.com/egnyte/gitlabform/issues/173)

Thanks to [@andrewjw](https://github.com/andrewjw) (Ocado Technology) for his contribution!

### 2.1.0

* **Added a feature to share groups with other groups, with optional enforcing.** Implements [#150](https://github.com/egnyte/gitlabform/issues/150)

Thanks to [@andrewjw](https://github.com/andrewjw) (Ocado Technology) for this contribution!

### 2.0.6

* Fixed incorrect subgroups list when requesting to process ALL_DEFINED. Completes the fix for [#221](https://github.com/egnyte/gitlabform/issues/221)

### 2.0.5

* *Really* fixed issue with `unprotect_branch_new_api`. Fixes [#219](https://github.com/egnyte/gitlabform/issues/219)
* Fixed call to a Merge Requests Approvers API endpoint removed in GitLab 13.11.0. Fixes [#220](https://github.com/egnyte/gitlabform/issues/220)
* Fixed potential security issue by enabling autoescaping when loading Jinja templates. (Bandit security tool issue [B701](https://bandit.readthedocs.io/en/latest/plugins/b701_jinja2_autoescape_false.html))

Thanks to [@Pigueiras](https://github.com/Pigueiras) for his contribution!

### 2.0.4

* Fixed issue with Push Rules when the project name contains a dot. Fixes [#224](https://github.com/egnyte/gitlabform/issues/224)
* Fixed calling to process a single subgroup (like: `gitlabform 'group/subgroup'`). Fixes [#221](https://github.com/egnyte/gitlabform/issues/221)

### 2.0.3

* Fixed issue with dry-run for Project Push Rules when the current config is empty (`None`). Fixes [#223](https://github.com/egnyte/gitlabform/issues/223)

### 2.0.2

* Fixed issue with `unprotect_branch_new_api`. Fixes [#219](https://github.com/egnyte/gitlabform/issues/219)
  (update: later it turned out that it was not really fixed in 2.0.2 but in 2.0.5 instead)

### 2.0.1

* Fixed issues with Jinja loader.
* Fixed calls to GitLab API that do not contain 'x-total-pages' header (gradually rolled out since [GitLab MR #23931](https://gitlab.com/gitlab-org/gitlab-foss/-/merge_requests/23931)).
* Start showing deprecation warning when using the old branch protection API config syntax.

Thanks to [@mkjmdski](https://github.com/mkjmdski) for his contribution!

(2.0.0post1-3 release is technically the same as 2.0.1 but was incorrectly versioned.)

### 2.0.0

(For a detailed info about changes in each RC of v2 please see the previous version of this changelog.)

* **Make deep merging of configuration actually work (breaking change).** Fixes [#197](https://github.com/egnyte/gitlabform/issues/197) (RC5)

* **Introduce config versioning (breaking change).** ...or rather a change to avoid breakage. New major releases of GitLabForm starting with v2 will look for `config_version` key in the config file. If it doesn't exist, or the version does not match expected then the app will exit to avoid applying unexpected configuration and allowing the user to update the configuration. (RC1)

* **New config syntax (breaking change).** All 3 levels under a common key `projects_and_groups`. It should contain a dict, where common config is under a special `"*"` key, group configs under keys like `group/*` and project configs under keys like `group/project`. This will allow introducing pattern matching in these keys and introducing support for multiple config files in the future releases. Partially implements [#138](https://github.com/egnyte/gitlabform/pull/138). (RC1)

* **Exit with code != 0 when any group/project processing was failed (breaking change).** This will allow you to notice problems when running the app from CI. Note that you can restore the old behaviour by running the app with `(...) || true`. Also **standardized exit codes.** Exit with 1 in case of input error (f.e. config file does not exist), with 2 in case of processing error (f.e. GitLab returns HTTP 500).Fixes [#153](https://github.com/egnyte/gitlabform/issues/153). (RC1)

* **Allow any case in groups and projects names (breaking change).** GitLab groups and projects names are case sensitive but you cannot create such entities that differ only in the case. There is also a distinction between a "name" and a "path" and they may differ in case... To make work with this easier GitLabForm now accepts any case for them in both config files as well as when provided as command line arguments. We also disallow such entities that differ only in case (f.e. `group/*` and `GROUP/*`) to avoid ambiguity. Fixes [#160](https://github.com/egnyte/gitlabform/issues/160). (RC2)

* **Ignore archived projects by default (breaking change).** This makes processing faster and output shorter. You can restore the previous behavior by adding `--include-archived-projects`/`-a` command line switch. Note that you have to do it if you want to unarchive archived projects! Fixes [#157](https://github.com/egnyte/gitlabform/issues/157) in (arguably) a more convenient way. (RC2)

* **Color output!** Implements [#141](https://github.com/egnyte/gitlabform/issues/141). (RC1)

* **Add diffing feature for secret variables.** (with values shown as hashes to protect the secrets from leaking). (RC6)

* **Added checking for invalid syntax in "members" section.** Defining groups or users directly under this key instead of under sub-keys "users" and "groups" will now trigger an immediate error. (RC5)

* **Add support for Python 3.9** (RC8)

* **Added Windows support.** Fixes [#206](https://github.com/egnyte/gitlabform/issues/206) (RC5)

* **Start processing at any group** using the new command line switch - `--start-from-group`/`-sfg`. Similar to `--start-from`/`-sf` switch that can be used for projects. (RC1)

* Start releasing pre-releases as Docker images. They have tags with specific versions, but not "latest" tag as it is reserved for new final releases. Implements [#201](https://github.com/egnyte/gitlabform/issues/201) (RC5)

* Prevent multiple email notifications from being sent when adding members to project. Fixes [#101](https://github.com/egnyte/gitlabform/issues/101) (RC6)

* Prevent project's Audit Events being filled in with "Added protected branch". Fixes [#178](https://github.com/egnyte/gitlabform/issues/178) (RC6)

* Fixed using "expires_at" for users. Fixes [#207](https://github.com/egnyte/gitlabform/issues/207) (RC6)

* Remove the need to add the `gitlab.api_version` configuration key. (RC1)

* **For Contributors** Make writing tests easier and the tests more robust. Deduplicate a lot of the boilerplate code, allow putting configs into the test methods and use pytest fixtures for easier setup and cleanup. This should fix issues with tests reported in [#190](https://github.com/egnyte/gitlabform/issues/190). Also stop storing any dockerized GitLab data permanently to avoid problems like [#196](https://github.com/egnyte/gitlabform/issues/196) and probably other related to failed dockerized GitLab upgrades. (RC3)

* **For Contributors** Rename "integration tests" to "acceptance tests". Because they ARE in fact acceptance tests. (RC3)

Thanks to [@amimas](https://github.com/amimas), [@weakcamel](https://github.com/weakcamel), [@kowpatryk](https://github.com/kowpatryk), [@ss7548](https://github.com/ss7548), [@houres](https://github.com/houres), [@Pigueiras](https://github.com/Pigueiras) and [@YuraBeznos](https://github.com/YuraBeznos) for their contributions!

### before 2.0.0

Please see [GitHub pre-2.0 releases' descriptions](https://github.com/egnyte/gitlabform/releases?after=v2.0.0rc1).
