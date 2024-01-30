Issue: When calling a callable workflow ("nested") from another callable workflow ("caller") in a matrix strategy, if there is a failure in the "nested" workflow and a user uses the line-item based re-run button in the Summary page, GitHub does not enforce any of the dependency requirements for all workflows.

Repro:
- Add the following repo Actions variables with the default values below
  - `FORCE_SLEEP` set to `true`
  - `SKIP_FAILURE` set to `false`
- Open a PR in a repo that contains the 3 workflows of this repo
- Observe
  - `job1` succeeds
  - `job2 / callable_job (a) / nested_callable_job` fails
  - `job2 / callable_job (b|c|d) / nested_callable_job`s are canceled
  - `job2` fails
  - `final` fails
- Change `SKIP_FAILURE` to `true`
- Click the re-run job button on the line for the failed `nested_callable_job` on the check summary page
[single line rerun](./images/single_line_rerun.png)
- Observe
  - Only `job2 / ... / nested_callable_job` is listed as planned to run
  - `job2 / callable_job (a) / nested_callable_job` now passes
  - `final` now passes even though the nested jobs for the other matrix strategy still are marked as canceled
