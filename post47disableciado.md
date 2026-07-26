# Disable continuous integration for an ADO pipeline

Recently I have been testing Azure DevOps pipelines for deploying IAC projects using Terraform. The default pipeline yaml file will have the following snippet of code

```
trigger:
  main
```
The above code will trigger the pipeline upon any commits to the **main** branch. 

However, I wanted to be able to manually trigger my pipeline as I'm doing various tests. I spent some time trying to find where in the GUI that I can disable CI for the pipeline and thought I should document it here for sharing.

## Select and Edit the pipeline

![adoci1.png](adoci1.png)


## Select the 3 ellipse on the top right side and choose Triggers

![adoci2.png](adoci2.png)


## Override the YAML configuration trigger

![adoci3.png](adoci3.png)

Select disable Continuous Integration and then Save
