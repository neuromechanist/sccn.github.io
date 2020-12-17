---
layout: default
title: b. Events
parent: 4. Import data
grand_parent: Tutorials
---
Importing event and epoch information
===========

<details open markdown="block">
  <summary>
    Table of contents
  </summary>
  {: .text-delta }
- TOC
{:toc}
</details>

EEGLAB counts records of the time and nature of experimental events to
analyze the EEG data. This section details how to load in event
information which coded in one of the data channels, stored in a Matlab
array or separate ascii file. When event information is read in, EEGLAB copies the resulting EEG.event structure to a back-up
(*ur*) copy, EEG.urevent and creates links from each event to the
corresponding urevent. This allows the user to select events based on
the previous (or future) event *context*, even *after* data containing
some events has been rejected from the data as described in the event scripting section of the tutorial.

Importing events from a data channel
------------

Often, information about experimental events are recorded onto one of
the rows (channels) of the EEG data matrix. Once more we create
simulated data to illustrate how to import events from a data channel.
Assuming an EEG dataset with 33 rows (channels), out of which the first
32 are channels and the last (33) is an event channel with values 1
(stimulus onset), 2 (subject response), and 0 (other), Matlab code for
generating such data follows (to test, copy and paste the code to the
Matlab command line):

```matlab
matlab
eegdata = rand(32, 256\*100); % 32 channels of random activity (100 s sampled at 256 Hz).
eegdata(33,\[10:256:256\*100\]) = 1; % simulating a stimulus onset every second
eegdata(33,\[100:256:256\*100\]+round(rand\*128)) = 2; % simulating reaction times about 500 ms after stimulus onsets
```

After copying the code above to Matlab and importing the array *eegdata*
into EEGLAB as a test dataset using menu item <font color=brown>File → Import data →
from ASCII/float file or Matlab array</font>, select
menu item <font color=brown>File → Import event info → from data
channel</font> to call function [pop_chanevent.m](http://sccn.ucsd.edu/eeglab/locatefile.php?file=pop_chanevent.m) .

![Image:Ii21pop_chanevent.jpg](/assets/images/Ii21pop_chanevent.jpg)

Enter *33* as the event channel and set the edge-extract type to *up
(leading)* (Note: place the mouse over the text *Transitions to extract*
to see contextual help).

Press *OK*. Now, the event information will have been imported into the
test EEGLAB dataset. At the same time, channel 33 will have been deleted
from the test data. Select menu item <font color=brown>Edit → Event
values</font> to inspect the imported event types and latencies.

Importing events from a Matlab array or text file
------------

Using the random EEG dataset created above, we import event information
stored in an ASCII text file,
[tutorial_eventtable.txt](http://sccn.ucsd.edu/eeglab/download/tutorial_eventtable.txt).
This text file is composed of three columns, the first containing the
latency of the event (in seconds), the second the type of the event, and
the third a parameter describing the event (for example, the position of
the stimulus). For example, the top lines of such a file might be:

<table>
<tr>
<td><strong>Latency</strong></td>
<td><strong>Type</strong></td>
<td><strong>Position</strong></td>
</tr>
<tr>
<td>1</td>
<td>target</td>
<td>1</td>
</tr>
<tr>
<td>2.3047</td>
<td>response</td>
<td>1</td>
</tr>
<tr>
<td>3</td>
<td>target</td>
<td>2</td>
</tr>
<tr>
<td>4.7707</td>
<td>response</td>
<td>2</td>
</tr>
<tr>
<td>5</td>
<td>target</td>
<td>1</td>
</tr>
<tr>
<td>6.5979</td>
<td>response</td>
<td>1</td>
</tr>
<tr>
<td>...</td>
<td>...</td>
<td>...</td>
</tr>
</table>

Select menu item <font color=brown>File → Import event info → Import
Matlab array or ASCII file</font>

![Image:Pop_importevent.jpg](/assets/images/Pop_importevent.jpg)

Browse for the tutorial text file [tutorial_eventtable.txt](http://sccn.ucsd.edu/eeglab/download/tutorial_eventtable.txt), set the number of header lines to 1
(for the first line of the file, which gives the column field names) and
set the input fields (i.e., the names associated with the columns in the
array) to *latency type position*. If these field names are quoted or
separated by commas, these extra characters are ignored. (NOTE: It is
NECESSARY to use the names *latency* and *type* for two of the fields.
These two field names are used by EEGLAB to extract, sort and display
events. These fields must be lowercase since Matlab is case sensitive.)
In this interactive window the input *Event indices* and checkbox
*Append events?* can be used to insert new events or replace a subset of
events with new events (for instance for large EEG files which may have
several event files).

### Important note about aligning events

An essential input above is *Align event latencies to data events* which
aligns the first event latency to the existing event latency and checks
latency consistency. A value of *NaN* (Matlab for not-a-number)
indicates that this option is ignored (as in the example above).
However, for most EEG data, the EEG is recorded with basic events stored
in an event channel (see Import events from a data channel above) for
instance. Detailed event information is recorded separately in a text
file: as a consequence the events recorded in the text file have to be
aligned with the events recorded in the EEG.

To do so, set the input for *Align event latencies to data events* to 0
if the first event in the text file correspond to the first event
recorded in the EEG (i.e., if the offset between the two is 0). Setting
this value to 1 indicates that event 1 in the event text file
corresponds to event number 2 in the EEG data. Here, negative values can
also be used to indicate that events in text file start before those
recorded in the EEG).

When aligning events, as shown in the following section, the function
displays the latencies of the two event types, so the user can check
that they are aligned based on his knowledge of the experiment (for
instance, there may be more events in the text file than recorded in the
EEG).
The last checkbox allow to automatically adjust the sampling rate of the
new events so they best align with the closest old event. This may take
into account small differences in sampling rates that could lead to big
differences by the end of the experiment (e.g., a 0.01% clock difference
during would lead to a 360-ms difference after one hour if not
corrected).

Importing events from a Presentation file
------------

EEGLAB can also import events saved using the Presentation software.
Sample files are available for download here:
[TEST.SMA](http://sccn.ucsd.edu/eeglab/download/TEST.SMA) (a SnapMaster
.SMA file) and [TEST.LOG](http://sccn.ucsd.edu/eeglab/download/TEST.LOG)
(a Presentation event log file). If you click on the links and a text file appear, right click on the link and use *Save link as* to save the files.

To test the use of these sample files, first import the .SMA data file
using menu item <font color=brown>File → Import data → From .SMA data
file</font>. Then select <font color=brown>File → Import event info →
from Presentation LOG. file</font> to import events from the
Presentation log file as shown below</font>



![Image:load_presentation_file.png](/assets/images/load_presentation_file.png)



Then the following window pops-up</font>


![Image:Pop_importpres2.jpg](/assets/images/Pop_importpres2.jpg)

Scroll file fields to select which field (i.e., file column) contain the
event type and which column contain the event latency. The default is
fine with this specific file, so simply press *OK*. Matlab then returns:

```matlab
Replacing field 'Event Type' by 'type' for EEGLAB compatibility
Replacing field 'Time' by 'latency' for EEGLAB compatibility
Renaming second 'Uncertainty' field
Reading file (lines): 6
Check alignment between pre-existing (old) and loaded event latencies:
Old event latencies (10 first): 10789 21315 31375 41902 51962 62489 …
New event latencies (10 first): 10789 21315 31376 41902 51963 62489 …
Best sampling rate ratio found is 0.9999895. Below latencies after adjustment
Old event latencies (10 first): 10789 21315 31376 41902 51963 62488 …
New event latencies (10 first): 10789 21315 31375 41902 51962 62489 …
Pop_importevent warning: 0/6 have no latency and were removed
eeg_checkset: value format of event field 'Duration' made uniform
eeg_checkset: value format of event field 'Uncertainty2' made uniform
eeg_checkset note: creating the original event table (EEG.urevent)
Done.
```

The function aligns the first event latency recorded in the Presentation
file to the first event latency recorded in the EEG in the SnapMaster
file. Check that the events recorded in the SnapMaster file have the
same latencies as the ones recorded in the .LOG presentation file. The
function then computes the best sampling rate ratio: this may account
for small differences in sampling rate that could lead to big
differences at the end of the experiment (e.g., 0.01% clock difference
during half an hour would lead to a 360-ms difference after one hour if
not corrected). Note that if the events are shifted (with respect to
events from the binary EEG file), it is always possible to suppress
events manually or to import the presentation file as a text file, as
described in the previous section. Note that some Presentation files
that contain comments at the end of the file may not be supported. If
you are not able to import a Presentation file, try removing any
comments from the end of the file. If it still does not work, try
importing the Presentation file as a text file as described in the
previous section.

Note: the presentation file contains more events (such as reaction time)
compared to the raw EEG data file (this is actually why we are importing
such file). The function will automatically ignore additional events
when events are aligned.

Importing E-Prime information files
------------

The E-prime format is highly configurable, so you may use the ASCII
importer to import data files. Use menu item <font color=brown>File → Import event info →
from E-Prime ASCII (text) file</font>, which is the same as calling the <font color=brown>Import data → From ASCII/float file or Matlab array</font> menu item. Configure the
interface with the name of the E-Prime columns to import the event file. It might be necessary in
some cases to export the E-Prime to a tab-delimited file first (for
example in a spreadsheet application) and edit some of the columns
information that might not be read correctly under Matlab. Send us your
E-Prime files (at eeglab at sccn.ucsd.edu) so we may tailor the ASCII
import menu for E-Prime files.

Importing Neuroscan .DAT information files
------------

A sample .DAT file [TEST.DAT](http://sccn.ucsd.edu/eeglab/download/TEST.DAT) associated a sample the continuous [TEST.CNT](http://sccn.ucsd.edu/eeglab/download/TEST.CNT) are available for download for testing purposes. Both the .DAT file and the .CNT files must contains the same number of events (in this case 100). After importing the CNT file into EEGLAB, to import the .DAT file linked , select
menu item <font color=brown>File → Import epoch info → From Neuroscan .DAT info file</font>. After selecting the file to import, a second window appears:

![Image:Pop_loaddat.gif](/assets/images/Pop_loaddat.gif)

In .DAT files, there must be a reaction time (in milliseconds) for each
epoch. However, depending on experiment design there may be no reaction
time in a given epoch. Then one has to use a code value for reaction
time latencies in these epochs. For instance, you might decide that a
value of *1000* (ms) would indicate that the subject did not respond.
(If all the epochs of the experiment already have a reaction time, do
not enter anything here.)

Importing epoch info (Matlab array or text file) into EEGLAB
------------

Importing epoch information means that data epochs have already been
extracted from the continuous EEG data, and that the Matlab array or
text epoch information file has one entry per epoch. To illustrate how
to import such a file or array, we will once more create some simulated
EEG data.

```matlab
eegdata = rand(32, 256, 10); % 32 channels, 256 time points per epoch, 10 epochs
```

Select menu item <font color=brown>File → Import data → From ascii/float data file or Matlab array</font>. Refer to the [previous section](http://localhost:4000/tutorials/Import/Importing_Continuous_and_Epoched_Data.html) of the tutorial. 

Note that the Matlab array, being 3-D, is
automatically imported as data epochs: the first dimension is
interpreted as data channels, the second as data points and the third as
data epochs or trials (e.g., our sample data matrix above contains 10
epochs). Let us imagine that our simulated EEG data came from a simple
stimulus/response task, with subject responses being either 'correct' or
'wrong' and response latencies recorded in milliseconds. Then the epoch
event file might look something like this:

|:----------------------------:|:------------------------------:|:---------------------------------------:|
|**Epoch** |**Response** |**Response_latency** |
|1         |  Correct    |                 502 |
|2         |  Correct    |                 477 |
|3         |  Correct    |                 553 |
|4         |  Correct    |                 612 |
|5         |   Wrong     |                 430 |
|6         |  Correct    |                 525 |
|7         |  Correct    |                 498 |
|8         |  Correct    |                 601 |
|9         |  Correct    |                 398 |
|10        |  Correct    |                 573 |


This file [tutorial_epoch.txt](http://sccn.ucsd.edu/eeglab/download/tutorial_epoch.txt) may
be downloaded (or copied from the array above in a text file). Then select
menu item <font color=brown>File → Import epoch info → from Matlab
array or ascii file</font>, bringing up the following window:

![Image:Ii33pop_inportepoch.jpg](/assets/images/Ii33pop_inportepoch.jpg)



Above, browse for the *tutorial_epoch.txt* file, set the input fields to
*epoch response rt* (where rt is an acronym for 'reaction time'). The
only one of these fields that contains latency information is *rt*, so
it is entered to the as input to the *Field name(s) containing
latencies* query box. This file (see above) has 1 header line, as we
need to specify in the *Number of file header lines to ignore* box.
Finally the reaction times are recorded in milliseconds, which we
indicate as *1E-3* (i.e., one-thousandth of a second). Note that the
last entry, *Remove old epoch ...*, allows the user to import other
information later if it is unset. Press *OK* when done.  Now select
menu item <font color=brown>Edit → Event values</font> to inspect what
happened to the reaction time information (use the arrow to move to the
second event):

![Image:Ii33pop_editeventvals.jpg](/assets/images/Ii33pop_editeventvals.jpg)

As shown above, when epoch information was imported, events with type
named *rt* were created and assigned a latency. If we had had several
columns containing latency information, the function would have created
several types.

Note: For convenience, standard epoch information is
available from the command line in the variable *EEG.epoch*. Also, event
information available in *EEG.event* can be used for script or command
line data processing. See the events script writing
tutorials for more information.

Exporting event information
---------------------------
Event information can also be exported to a .csv file (a plain text file
containing comma-separated values; a tab is used as delimiter). This
file can be opened with any text editor, OpenOffice Calc, or Microsoft
Excel, for example. To export all events of the currently loaded EEG
file, select <font color=brown>File → Export → Events to text file</font>. A dialog window pops
up asking for the name and location of the .csv file. The first row of
the file contains the names of the event fields. Note that there is
additional column "number", which is not an event field.

If you want more control over export options, you should use the command
line version of this tool. In the MATLAB command window, enter the
following code to achieve the same result as before with the GUI:

```matlab
events = eeg_eventtable(EEG, 'exportFile', 'test.csv');
````

In addition, a table with all events is displayed in the command window
by default. The additional (first) index column can be disabled, and the time
unit of the latency and duration event fields can be set to seconds or
samples (default). The event structure is stored in a cell array for
convenient access. See the help of the [eeg_eventtable.m](http://sccn.ucsd.edu/eeglab/locatefile.php?file=eeg_eventtable.m) function for additional details. 