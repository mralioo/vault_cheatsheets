Hydra is highly configurable. Many of its aspects and subsystems can be configured, including:

- The Launcher
- The Sweeper
- Logging
- Output directory patterns
- Application help (--help and --hydra-help)


## Accessing the Hydra config
The Hydra config is large. To reduce clutter in your own config it's being deleted from the config object Hydra is passing to the function annotated by `@hydra.main()`.

```python
config_name: ${hydra:job.name}
```


### hydra.job:[​](https://hydra.cc/docs/configure_hydra/intro/#hydrajob "Direct link to heading")

The **hydra.job** node is used for configuring some aspects of your job. Below is a short summary of the fields in **hydra.job**. You can find more details in the [Job Configuration](https://hydra.cc/docs/configure_hydra/job/) page.

Fields under **hydra.job**:

- **name** : Job name, defaults to the Python file name without the suffix. can be overridden.
- **override_dirname** : Pathname derived from the overrides for this job
- **chdir**: If `True`, Hydra calls `os.chdir(output_dir)` before calling back to the user's main function.
- **id** : Job ID in the underlying jobs system (SLURM etc)
- **num** : job serial number in sweep
- **config_name** : The name of the config used by the job (Output only)
- **env_set**: Environment variable to set for the launched job
- **env_copy**: Environment variable to copy from the launching machine
- **config**: fine-grained configuration for job

### hydra.run:[​](https://hydra.cc/docs/configure_hydra/intro/#hydrarun "Direct link to heading")

Used in single-run mode (i.e. when the `--multirun` command-line flag is omitted). See [configuration for run](https://hydra.cc/docs/configure_hydra/workdir/#configuration-for-run).

- **dir**: used to specify the output directory.

### hydra.sweep:[​](https://hydra.cc/docs/configure_hydra/intro/#hydrasweep "Direct link to heading")

Used in multi-run mode (i.e. when the `--multirun` command-line flag is given) See [configuration for multirun](https://hydra.cc/docs/configure_hydra/workdir/#configuration-for-multirun).

- **dir**: used to specify the output directory common to all jobs in the multirun sweep
- **subdir**: used to specify the a pattern for creation of job-specific subdirectory

### hydra.runtime:[​](https://hydra.cc/docs/configure_hydra/intro/#hydraruntime "Direct link to heading")

Fields under **hydra.runtime** are populated automatically and should not be overridden.

- **version**: Hydra's version
- **cwd**: Original working directory the app was executed from
- **output_dir**: This is the directory created by Hydra for saving logs and yaml config files, as configured by [customizing the working directory pattern](https://hydra.cc/docs/configure_hydra/workdir/).
- **choices**: A dictionary containing the final config group choices.
- **config_sources**: The final list of config sources used to compose the config.

### hydra.overrides[​](https://hydra.cc/docs/configure_hydra/intro/#hydraoverrides "Direct link to heading")

Fields under **hydra.overrides** are populated automatically and should not be overridden.

- **task**: Contains a list of the command-line overrides used, except `hydra` config overrides. Contains the same information as the `.hydra/overrides.yaml` file. See [Output/Working directory](https://hydra.cc/docs/tutorials/basic/running_your_app/working_directory/).
- **hydra**: Contains a list of the command-line `hydra` config overrides used.

### hydra.mode[​](https://hydra.cc/docs/configure_hydra/intro/#hydramode "Direct link to heading")

See [multirun](https://hydra.cc/docs/tutorials/basic/running_your_app/multi-run/) for more info.

### Other Hydra settings[​](https://hydra.cc/docs/configure_hydra/intro/#other-hydra-settings "Direct link to heading")

The following fields are present at the top level of the Hydra Config.

- **searchpath**: A list of paths that Hydra searches in order to find configs. See [overriding `hydra.searchpath`](https://hydra.cc/docs/advanced/search_path/#overriding-hydrasearchpath-config)
- **job_logging** and **hydra_logging**: Configure logging settings. See [logging](https://hydra.cc/docs/tutorials/basic/running_your_app/logging/) and [customizing logging](https://hydra.cc/docs/configure_hydra/logging/).
- **sweeper**: [Sweeper](https://hydra.cc/docs/tutorials/basic/running_your_app/multi-run/#sweeper) plugin settings. Defaults to basic sweeper.
- **launcher**: [Launcher](https://hydra.cc/docs/tutorials/basic/running_your_app/multi-run/#launcher) plugin settings. Defaults to basic launcher.
- **callbacks**: [Experimental callback support](https://hydra.cc/docs/experimental/callbacks/).
- **help**: Configures your app's `--help` CLI flag. See [customizing application's help](https://hydra.cc/docs/configure_hydra/app_help/).
- **hydra_help**: Configures the `--hydra-help` CLI flag.
- **output_subdir**: Configures the `.hydra` subdirectory name. See [changing or disabling the output subdir](https://hydra.cc/docs/tutorials/basic/running_your_app/working_directory/#changing-or-disabling-hydras-output-subdir)
- **verbose**: Configures per-file DEBUG-level logging. See [logging](https://hydra.cc/docs/tutorials/basic/running_your_app/logging/).


# Instantiating objects with Hydra
https://hydra.cc/docs/advanced/instantiate_objects/overview/#partial-instantiation

## Links
[Hydra — A fresh look at configuration for machine learning projects](https://medium.com/pytorch/hydra-a-fresh-look-at-configuration-for-machine-learning-projects-50583186b710)
[Creating Configurable Data Pre-Processing Pipelines by Combining Hydra and Sklearn](https://medium.com/beyondminds/creating-configurable-data-pre-processing-pipelines-by-combining-hydra-and-sklearn-812065c9ab64)
