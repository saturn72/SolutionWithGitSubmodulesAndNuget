# Visual Studio Solution with Nuget git submodules
One of the issues we faced while developing our system was how can we use the submodule feature and restore nuget packages to different locations.

Since nuget restore the packages to the solution level, we were not able to properly compile the submodules, as these were not located in the "hosted" solution.

To be more clear:

Nuget restores all solution packages to <code>&lt;solution_root_directory&gt;\packages</code> while the submodule expects all its referenced assemblies to be located in <code>&lt;submodules_solution_root_directory&gt;\packages</code>. Since the solution and submodules cannot reside in the same directory - the solution is failed to compile, due to missing references.

We found quite convenience solution for this problem by using symbolic link feature (Windows operating system). This enables you to place "live" reference to the <code>&lt;solution_root_directory&gt;\packages</code> directory in the submodule root level.

2 important things to bare in mind:
<ol>
	<li>The creation of the symbolic link should be done separately from the code executed (we are using Post-build-event of the startup project)</li>
	<li>All submodules  root directories need to be known</li>
</ol>
This is the general pattern of the post-build event (batch syntax):

```batch
SET sourceDir=$(SolutionDir)packages
SET destDir=$(SolutionDir)..\..\<all_submodules_root_directory>\<specific_submodule_directory>\<solution_directory>\packages
SET remDirCmd=rmDir %destDir%

MKLINK /j %destDir% %sourceDir%
```
## Example

```batch
SET sourceDir=$(SolutionDir)packages
SET destDir=$(SolutionDir)..\..\submodules\saturn72.extensions\src\packages
SET remDirCmd=rmDir %destDir%

MKLINK /j %destDir% %sourceDir%
```

A sample project was uploaded to our <a href="https://github.com/saturn72">github </a>space, so feel free to check it out: <a href="https://github.com/saturn72/SolutionWithGitSubmodulesAndNuget">SolutionWithGitSubmodulesAndNuget</a>
