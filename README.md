
# ErrorLog
Error Logger for LabVIEW with a simple interface and flexible implementation.  Designed for
compatability in a Real-Time environment.  

Features:
* Configurable file/path name
* Optional suppression of successively duplicate errors
* Round-robin output to a user-configurable number of files
* Log File rotated on both Class instantiation and configurable file size
* Atomic file writes - reference is held open just long enough to write to the log file

The libray maintains a set of log files sorted by modification time.  It will start logging to the oldest file
to start, and will change files once a file has exceeded the configurable size limit.

Files are named after the configurable parameters with a number appended.

e.g. Errors_1.log, Errors_2.log, ... Errors_5.log

Finally, this library contains a few helper functions for interfacing with a UI that retain
a few of the most recently logged errors - both new and seen, as well as a dialog for
viewing/acknowleging the most recent errors.

---

# API
### ErrorLog_Create.vi
> Construct the Error Logger

This builds the initial set of log files.  The library will start logging to the oldest existing file or the first unused file.

| Parameter | Default | Description |
| --- | --- | --- |
| ErrorLog Folder | "C:\" | Folder into which the log files should be stored |
| ErrorLog FileName | "Errors" | Prefix for the log files |
| Supress Consecutive Duplicates | True | Discard consecutive duplicate errors |
| Max Log Files | 5 | How many files to use in the rotation |
| Max File Size | 10 MB | File Size at which should rotation occur |

### ErrorLog_LogError.vi
> Log an error

Log an incoming error and clean it so the program can continue.

| Parameter | Description |
| --- | --- |
| Error to Log | The main error wire that will contain the operational status of the calling routine |
| Context | What is/was going on that caused the error.  Ideal for describing the file that couldn't be opened or socket number in use or instrument, etc. |
| Cleaned Error | The main error wire passed back out to your application now cleaned of the error condition |
| error in (no error) | Reserved input.  Don't pass your application's errors in here! |

Errors are written in the following format:

> &lt;Date Time&gt;[Context #Number] Message

| Field | Description |
| --- | --- |
| Date | The 'date string' from LabVIEW's "Get Date/Time String" primitive |
| Time | The 'time string' from LabVIEW's "Get Date/Time String" primitive |
| Context | Context passed in |
| Number | The Error Number as decimal |
| Message | The Error Source |

### ErrorLog_GetErrors.vi
> Has something gone awry?

Returns a list of the most recent new errors and the most recent acknowleged errors.

| Parameter | Description |
| --- | --- |
| New Errors | True if errors have been logged since the last call to this VI |
| Uncleared Errors | Have errors been logged that have not been cleared/acknowleged |
| Errors since last call | A roll-up of the last few errors since the last call to this VI |
| Errors since last clear | A roll-up of the last few errors that have not been cleared/acknowleged |

### ErrorLog_ClearErrors.vi
> It's all better now

Clear logged errors and reset the notification.

NOTE: this does not empty/clear errors logged to file, only the notification that errors have occurred.

*No Parameters.*

### ErrorLog_ShowErrorDialog.vi
> What just happened?

Show a dialog that displays the latest errors that have been logged.  This dialog is modal and updates in
real-time.  Simply opening this dialog will clear the 'New Errors' notification.  The dialog also allows
the user to Clear logged errors similarly to ErrorLog_ClearErrors.vi

*No Parameters.*

### ErrorLog_HasErrors.vi
> Have any errors been logged recently?

Gets the status of the New Errors notification without clearing it.

| Parameter | Description |
| --- | --- |
| Has Errors | True if errors have been logged but not viewed/cleared |

### ErrorLog_HasAnyErrors.vi
> Have any errors been logged - ever?

Returns if any errors have been logged that have not been already cleared.

| Parameter | Description |
| --- | --- |
| Has Errors | True if errors have been logged but not viewed/cleared |
| Errors | A roll-up of the last few errors that have not been cleared/acknowleged |

### ErrorLog_ClearAllErrors.vi
> Nothing to see here, move along.

Clears all error notifications without affecting the log file(s) on disk.

*No Parameters.*

