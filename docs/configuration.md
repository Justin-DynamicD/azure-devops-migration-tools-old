# Configuration
Azure DevOps Migration Tools are mainly powered by configuration which allows you to control most aspects of the execution flow.

## Configuration tool
If you run `vstssyncmigrator.exe init` you will be launched into a configuration tool that will generate a default file. Using the `init` command will create a `configuration.yml` file in the
working directory. At run time you can specify the configuration file to use.

**Note:** Azure DevOps Migration Tools do not ship with internal default configuration and will not function without one.

To create your config file just type `vstssyncmigrator init` in the directory that you unziped the tools and a minimal `configuration.json` configuration
file will be created. Modify this as you need.

Note that the generated file show all the possible options, you configuration file will probably only need a subset of those shown.

## Global configuration
The global configuration created by the `init` command look like this:

```json
{
  "Version": "0.0.0",
  "TelemetryEnableTrace": false,
  "workaroundForQuerySOAPBugEnabled": false,
  "Source": {
    "Collection": "https://dev.azure.com/psd45",
    "Name": "DemoProjs",
    "ReflectedWorkItemIDFieldName": "TfsMigrationTool.ReflectedWorkItemId"
  },
  "Target": {
    "Collection": "https://dev.azure.com/psd46",
    "Name": "DemoProjt",
    "ReflectedWorkItemIDFieldName": "ProcessName.ReflectedWorkItemId"
  },
  "FieldMaps": [],
  "WorkItemTypeDefinition": {
    "Bug": "Bug",
    "Product Backlog Item": "Product Backlog Item"
  },
  "Processors": [
    {
      "ObjectType": "VstsSyncMigrator.Engine.Configuration.Processing.NodeStructuresMigrationConfig",
      "PrefixProjectToNodes": false,
      "Enabled": false,
      "BasePaths": [
        "Product\\Area\\Path1",
        "Product\\Area\\Path2"
      ]
    },
    {
      "ObjectType": "VstsSyncMigrator.Engine.Configuration.Processing.WorkItemMigrationConfig",
      "ReplayRevisions": true,
      "PrefixProjectToNodes": false,
      "UpdateCreatedDate": true,
      "UpdateCreatedBy": true,
      "UpdateSourceReflectedId": true,
      "BuildFieldTable": false,
      "AppendMigrationToolSignatureFooter": false,
      "QueryBit": "AND [TfsMigrationTool.ReflectedWorkItemId] = '' AND  [Microsoft.VSTS.Common.ClosedDate] = '' AND [System.WorkItemType] IN ('Shared Steps', 'Shared Parameter', 'Test Case', 'Requirement', 'Task', 'User Story', 'Bug')",
      "OrderBit": "[System.ChangedDate] desc",
      "Enabled": false,
      "LinkMigration": true,
      "AttachmentMigration": true,
      "AttachmentWorkingPath": "c:\\temp\\WorkItemAttachmentWorkingFolder\\",
      "FixHtmlAttachmentLinks": false,
      "WorkItemCreateRetryLimit": 5,
      "FilterWorkItemsThatAlreadyExistInTarget": true
    }
  ]
}


```

And the description of the available options are:

### TelemetryEnableTrace
Allows you to submit trace to Application Insights to allow the devellpment team to diagnose any issues that may be found. If you are submitting a support ticket then please include the Session GUID found in your log file for that run. This will help us find the problem.

**note:** All exceptions that you encounter will surface inside of Visual Studio as the developers are working on the source. This will make sure that they tackle issues as they arise.

### Source & Target
Both the `Source` and `Target` entries hold the collection URL and the Team Project name that you are connecting to. The `Source` is where the tool will read the data to migrate. The `Target` is where the tool will write the data.

### ReflectedWorkItemIDFieldName

This is the field that will be used to store the state for the migration . See [Server Configuration](server-configuration.md)  

### NodeStructuresMigrationConfig
You can specify BasePaths for Areas/Iterations to migrate. The area/iteration has to start with that string to be eligible for migration.
E.g. BasePath = "Product\\Area\\Path1"

With existing areas:
"Product\\Area\\Path1\\TestArea"
"SomeOtherProduct\\Area\\Path1\TestArea"
"Product\\OtherArea\\Path1\\TestArea"

only the first one matches the BasePath "Product\\Area\\Path1" and would be migrated, the other ones are ignored.