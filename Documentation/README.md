	# BIM Collaboration Format v2.1 Technical Documentation
![BCF](https://raw.githubusercontent.com/buildingSMART/BCF-XML/master/Icons/BCFicon128.png "The BCF logo")

Authors:

* Pasi Paasiala, Solibri (BCF 1.0 / BCF 2.0 / BCF 2.1)
* Juha Laukala, Tekla (BCF 1.0)
* Lassi Lifländer, Tekla (BCF 1.0)
* Klaus Linhard, IABI (BCF 2.0 / BCF 2.1)
* Erik Pijnenburg, Kubus (BCF 2.0)
* Léon van Berlo, TNO / open source BIM Collective (BCF 2.0)
* Jon Anders Sollien, bimsync (BCF 2.0 / BCF 2.1)

### Terms and Abbreviations

GUID
Globally Unique Identifier: http://en.wikipedia.org/wiki/Globally_Unique_Identifier
IfcGuid
Globally Unique Identifier in the IFC data. This format is used only when referring to objects in IFC datasets.


|       |           |
| ------------- |:-------------|
| BCF      | BIM Collaboration Format |
| BCF file     | File in BIM Collaboration Format     |
| Topic | One topic, such as a problem in the design, described in BCF file    |
| GUID |Globally Unique Identifier: http://en.wikipedia.org/wiki/Globally_Unique_Identifier

### Background
* This document describes the BCF format that is used to exchange topics, such as, issues, scenes, etc. between different BIM software.

### BCF file structure
A BCF file is a zip containing one folder for each topic with its file extension "bcfzip" for BCFv1.0 and BCFv2.0. The file extension "bcf" is introduced since BCFv2.1.
The root of the BCF zip contains the following files.

* project.bcfp (optional)
    - An XML file referencing the extension.xsd to a project. The schema for this file is project.xsd.
* bcf.version
	* An XML file following the version.xsd schema with information of the BCF schema used. The file content should be identical to the contents of [bcf.version](bcf.version "bcf.version")

It is possible to store additional files in the BCF zip container as documents. These files can be referenced by other files via their relative paths. It is recommended to put them in a folder called `Documents` in the root folder of the zip archive.

### Topic folder structure inside a BCFzip archive

The folder name is the GUID of the topic. This GUID is in the UUID form. The folder contains the following files:

* markup.bcf
    * An XML file following the markup.xsd schema that is described below.
* viewpoint.bcfv
    * An XML file following the visinfo.xsd schema that is described below.
    * Multiple viewpoints are possible since BCF 2.0. Names of these files are not predefined. Note: One viewpoint needs to be be named viewpoint.bcfv even in the case of multiple viewpoints.
* snapshot.png 
    *  A snapshot related to the topic (for compatibility with BCF 1.0).
Multiple snapshots are possible since BCF 2.0. Names of these files are not predefined. Note: One snapshot needs to be named snapshot.png even in the case of multiple viewpoints.


*Note: The elements in the XML files must appear in the order given in the schemas and described below.*

## Project (.bcfp) file

The project file contains reference information about the project the topics belong to.



 Attribute | Optional | Description |
:-----------|:------------|:------------:
 ProjectId  |        Yes |     ProjectId of the project

 In addition it has the following nodes:


 Element | Optional | Description |
:-----------|:------------|:------------
Name | Yes | Name of the project.
ExtensionSchema| No | URI to the extension schema.



## Markup (.bcf) file
The markup file contains textual information about the topic.

### Header
Header node contains information about the IFC files relevant to this topic. It has one attribute, ProjectGuid, which is the project GUID. Header has also a list of File nodes.

Each File node has the following attributes:


 Attribute | Optional | Description |
:-----------|:------------|:------------
 IfcProject  |        Yes |     IfcGuid Reference to the project to which this topic is related in the IFC file
IfcSpatialStructureElement | Yes | IfcGuid Reference to the spatial structure element, e.g. IfcBuildingStorey, to which this topic is related.
isExternal | Yes | Is the IFC file external or within the bcfzip. (Default = true).

In addition File has the following nodes:

 Attribute | Optional | Description |
:-----------|:------------|:------------
Filename | Yes | The BIM file related to this topic.
Date | Yes | Date of the BIM file.
Reference | Yes | URI to IfcFile. <br> IsExternal=false “..\example.ifc“ (within bcfzip) <br> IsExternal=true  “https://.../example.ifc“

### Topic
Topic node contains reference information of the topic. It has one attribute, Guid, which is the topic GUID. 


 Attribute | Optional | Description |
:-----------|:------------|:------------
Guid | No | Guid of the topic
TopicType | Yes | Type of the topic (Predefined list in “extension.xsd”)
TopicStatus | Yes | Type of the topic (Predefined list in “extension.xsd”)

In addition it has the following nodes:


 Element | Optional | Description |
:-----------|:------------|:------------
ReferenceLink | Yes | List of references to the topic, for example, a work request management system or an URI to a model.
Title | No | Title of the topic.
Priority | Yes | Topic priority. The list of possible values are defined in the extension schema.
Index | Yes | Number to maintain the order of the topics. 
Labels | Yes | Tags for grouping Topics. The list of possible values are defined in the extension schema.
CreationDate | No | Date when the topic was created.
CreationAuthor | No | User who created the topic.
ModifiedDate | Yes | Date when the topic was last modified. Exists only when Topic has been modified after creation.
ModifiedAuthor | Yes | User who modified the topic. Exists only when Topic has been modified after creation.
DueDate | Yes | Date until when the topics issue needs to be resolved.
AssignedTo | Yes | The user to whom this topic is assigned to. Recommended to be in email format. The list of possible values are defined in the extension schema.
Description | Yes | Description of the topic.
Stage | Yes | Stage this topic is part of (Predefined list in “extension.xsd”).

### BimSnippet (optional)
BimSnippet is an additional file containing information related to one or multiple topics. For example, it can be an IFC file containing provisions for voids.

 Attribute | Optional | Description |
:-----------|:------------|:------------
SnippetType | No | Type of the Snippet (Predefined list in “extension.xsd”)
IsExternal | Yes | Is the BimSnippet external or within the bcfzip. <br> (Default = false).

 Element | Optional | Description |
:-----------|:------------|:------------
Reference | No | URI to BimSnippet. <br> IsExternal=false  “..\snippetExample.ifc“ (within bcfzip) <br> IsExternal=true  “https://.../snippetExample.ifc“
ReferenceSchema | Yes | URI to BimSnippetSchema (always external)


### DocumentReference (optional)
DocumentReference provides a means to associate additional payloads or links with topics. The references may point to a file within the .bcfzip or to an external location.

Attribute | Optional | Description |
:-----------|:------------|:------------
Guid | Yes | Guid attribute for identifying it uniquely
IsExternal | Yes | Is the Document external or within the bcfzip. <br> (Default = false).

 Element | Optional | Description |
:-----------|:------------|:------------
ReferencedDocument | Yes | URI to document. <br> IsExternal=false  “..\exampleDoc.docx“ (within bcfzip) <br> IsExternal=true  “https://.../ exampleDoc.docx“
Description | Yes | Description of the document


### RelatedTopic (optional)
Relation between topics (Clash -> PfV -> Opening)

Attribute | Optional | Description |
:-----------|:------------|:------------
RelatedTopic/GUID | Yes | List of GUIDs of the referenced topics.


### Comment
The markup file can contain comments related to the topic. Their purpose is to record discussion between different parties related to the topic. Comment has also the Guid attribute for identifying it uniquely. In addition, it has the following nodes:

Element | Optional | Description |
:-----------|:------------|:------------
Date | No | Date of the comment
Author |No | Comment author
Comment | No | The comment text
Viewpoint | Yes | Back reference to the viewpoint GUID.
ModifiedDate | Yes | The date when comment was modified
ModifiedAuthor | Yes | The author who modified the comment

### Viewpoints
The markup file can contain multiple viewpoints related to one or more comments. A viewpoint has also the Guid attribute for identifying it uniquely. In addition, it has the following nodes:

Element | Optional | Description |
:-----------|:------------|:------------
Viewpoint | Yes | Filename of the viewpoint (.bcfv)
Snapshot | Yes | Filename of the snapshot(.png)
Index | Yes | Parameter for sorting

Viewpoints are immutable, therefore they should never be changed once created. If new comments on a topic require different visualization, new viewpoints should be added.

## Visualization information (.bcfv) file
The visualization information file contains information of components related to the topic, camera settings, and possible markup and clipping information.

### Components

The components node contains a set of Component references. The numeric values in this file are all given in fixed units (meters for length and degrees for angle). Unit conversion is not required, since the values are not relevant to the user. The components node has also the DefaultVisibility attribute which indicates true or false for all components of the viewpoint.

The `Components` element contains the following properties.
* `ViewSetupHints` to describe the visualization settings that were used in the application creating the viewpoint
* `Selection` to list components of interest
* `Visibility` to describe default visibility and exceptions
* `Coloring` to convey coloring options for displaying components

#### ViewSetupHints
This element contains information about the default visibility for elements of certain types (`SpacesVisible`, `SpaceBoundariesVisible` and `OpeningsVisible`) that should be applied if not stated otherwise.

#### Selection
The `Selection` element lists all components that should be either highlighted or selected when displaying a viewpoint.

**Optimization Rules**

BCF is suitable for selecting a few components. A huge list of selected components causes poor performance. All clients should follow this rule:

* If the size of the selected components is huge (approximately 1000 components), alert the user and give him the opportunity to modify the visibility.

#### Visibility
The `Visibility` element states the components `DefaultVisibility` and lists all `Exceptions` that apply to specific components.

**Optimization Rules**

BCF is suitable for hiding/showing a few components. A huge list of hidden/shown components causes poor performance. All clients should follow these rules:

* If the list of hidden components is smaller than the list of visible components: set default_visibility to true and put the hidden components in exceptions.
* If the list of visible components is smaller or equals the list of hidden components: set default_visibility to false and put the visible components in exceptions.
* If the size of exceptions is huge (approximately 1000 components), alert the user and give him the opportunity to modify the visibility.

#### Coloring
The `Coloring` element lists colors and a list of associated components that should be displayed with the specified color when displaying a viewpoint. The color is given in ARGB format. Colors are represented as 6 or 8 hexadecimal digits. If 8 digits are present, the first two represent the alpha (transparency) channel. For example, `40E0D0` would be the color <span style="color:#40E0D0;";>Turquoise</span>. [More information about the color format can be found on Wikipedia.](https://en.wikipedia.org/wiki/RGBA_color_space)

**Optimization Rules**

BCF is suitable for coloring a few components. A huge list of components causes poor performance. All clients should follow this rule:

* If the size of colored components is huge (approximately 1000 components), alert the user and give him the opportunity to modify the coloring.

#### Component

A component has the following attributes:

Attribute | Optional | Description |
:-----------|:------------|:------------
IfcGuid | No | The IfcGuid of the component
OriginatingSystem | Yes | Name of the system in which the component is originated
AuthoringToolId | Yes | System specific identifier of the component in the originating BIM tool

### OrthogonalCamera (optional)
This element describes a viewpoint using orthogonal camera. It has the following elements:

Element | Optional | Description |
:-----------|:------------|:------------
CameraViewPoint | No | Camera location
CameraDirection | No | Camera direction
CameraUpVector | No | Camera up vector
ViewToWorldScale | No | Scaling from view to world

### PerspectiveCamera (optional)
This element describes a viewpoint using perspective camera. It has the following elements:

Element | Optional | Description |
:-----------|:------------|:------------
CameraViewPoint | No | Camera location
CameraDirection | No | Camera direction
CameraUpVector | No | Camera up vector
FieldOfView | No | Camera’s field of view angle in degrees.

The `FieldOfView` is currently restricted to a value between 45 and 60 degrees. There may be viewpoints that are not within this range, therefore imports should be expecting any values between 0 and 360 degrees. The limitation will be dropped in the next schema release. `FieldOfView` represents the horizontal field of view.

### Lines (optional)
Lines can be used to add markup in 3D. Each line is defined by three dimensional Start Point and End Point. Lines that have the same start and end points are to be considered points and may be displayed accordingly.

### ClippingPlanes (optional)
ClippingPlanes can be used to define a subsection of a building model that is related to the topic. Each clipping plane is defined by Location and Direction.

### Bitmap (optional)
A list of bitmaps can be used to add more information, for example, text in the visualization. It has the following elements:


Element | Optional | Description |
:-----------|:------------|:------------
Bitmap | No | Format of the bitmap (PNG/JPG)
Reference | No | Name of the bitmap file in the topic folder
Location | No | Location of the center of the bitmap in world coordinates
Normal | No | Normal vector of the bitmap
Up | No | Up vector of the bitmap

## Implementation Agreements
Since BCF 2.0 is compatible with version 1.0, there are some ambiguities in the implementation. The following agreements are written to clarify the implementation.

### One to Many Mapping between Viewpoints and Comments
The schema would allow to have many to many mapping between viewpoints and comments. This is not allowed. A viewpoint can have multiple comments, but a comment can only refer to one viewpoint.

### Status and VerbalStatus to be Phased out
Status and Verbal Status of Comment will be phased out and replaced by TopicStatus and TopicType in Topic. 

When interpreting BCF 1.0 files use the following logic:

- use Status of most recent comment as value of TopicType
- use Verbalstatus of most recent comment as TopicStatus.

When interpreting BCF 2.0 or higher files: VerbalStatus and Status on comment level should all be neglected if TopicStatus and TopicType are present in Topic.

When writing BCF 2.0 or higher files:

- write the current type and status to Topic's TopicType and TopicStatus
- write Status and VerbalStatus at Comment level for backward compatibility.

### .BCFZIP encoding guide
 
Software tool vendors are encouraged to follow the following guidelines to ensure that the .BCFZIP files produced by their software can be correctly exchanged with other tools.

####  Use forward slash as path separator

The forward slash (“/”) should be used as a path separator rather than the backslash (“\”). Windows developers should consult the following MSDN page when attempting to follow this guideline: https://msdn.microsoft.com/en-us/library/mt712573(v=vs.110).aspx


| Correct |  Incorrect |
|---------|------------|
| `742b0df3-9a99-4da3-95ad-171e282f8122/snapshot.png` |  `742b0df3-9a99-4da3-95ad-171e282f8122\snapshot.png` |

#### Create directory (folder) entries in the zip file's central directory

When creating the zip archive make sure to create directory (folder) entries in the .BCFZIP file’s central directory.

**Example of a zip file with directory entries (Correct)**

```
Archive:  correctly_encoded_file_with_directory_entries.bcfzip
 Length      Date    Time    Name
---------  ---------- -----   ----
     217  2015-02-18 09:12   bcf.version
       0  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/
    1782  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/markup.bcf
     565  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/viewpoint.bcfv
  181641  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/snapshot.png
---------                     -------
  184205                     5 files
```

**Example of a zip file without directory entries (Incorrect)**
```
Archive:  incorrect_file_without_directory_entries.bcfzip
 Length      Date    Time    Name
---------  ---------- -----   ----
   35525  2015-02-25 09:28   742b0df3-9a99-4da3-95ad-171e282f8122/snapshot.png
     681  2015-02-25 09:28   742b0df3-9a99-4da3-95ad-171e282f8122/viewpoint.bcfv
    1057  2015-02-25 09:28   742b0df3-9a99-4da3-95ad-171e282f8122/markup.bcf
     119  2015-02-25 09:28   bcf.version
---------                     -------
   37382                     4 files
```


#### How to verify

##### On windows 7 using 7zip and the windows command prompt

1. Download and install 7zip from http://www.7-zip.org/download.html
2. On the start menu type ‘cmd’ and select cmd.exe
3. Execute the following:

`“c:\Program Files\7-Zip\7z.exe"  l $PATH_TO_BCFZIP`

**Example output**
```
7-Zip [64] 9.20  Copyright (c) 1999-2010 Igor Pavlov  2010-11-18

 Listing archive: E:\BIM\bcf files\bulkExport_1topic.bcfzip

 --
 Path = #### Path to the bcfzip file
 Type = zip
 Physical Size = 183599

    Date      Time    Attr         Size   Compressed  Name
 ------------------- ----- ------------ ------------  ------------------------
 2015-02-18 09:12:40 .....          217          161  bcf.version
 2015-02-18 09:12:40 D....            0            0  bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e
 2015-02-18 09:12:40 .....         1782          632  bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e\markup.bcf
 2015-02-18 09:12:40 .....          565          336  bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e\viewpoint.bcfv
 2015-02-18 09:12:40 .....       181641       181678  bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e\snapshot.png
 ------------------- ----- ------------ ------------  ------------------------
                                 184205       182807  4 files, 1 folders

```

##### On Unix or Linux shell

1. Start your favourite shell and execute the following:

``` $ unzip -l $PATH_TO_BCFZIP ```

**Example output**
```
Archive:  /mnt/engineering/BIM/bcf files/bulkExport_1topic.bcfzip
  Length      Date    Time    Name
---------  ---------- -----   ----
      217  2015-02-18 09:12   bcf.version
        0  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/
     1782  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/markup.bcf
      565  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/viewpoint.bcfv
   181641  2015-02-18 09:12   bff0f7ed-6b0d-4aad-ab28-401cc1db7f6e/snapshot.png
---------                     -------
   184205                     5 files
```

