# Triggers

## On Delete Triggers

```
CREATE TRIGGER CustomerTrackingLogging_Delete
AFTER DELETE
ON CustomerTracking
BEGIN
INSERT INTO CustomerTracking_History (MainID_Old,MainID_New,CustomerName_Old,CustomerName_New,CustomerAddress_Old,CustomerAddress_New,ActionTaken,Modified) VALUES (OLD.MainID,NULL,OLD.CustomerName,NULL,OLD.CustomerAddress,NULL,'Delete',datetime('NOW'));
END

```

  

## On Update Triggers

```
CREATE TRIGGER CustomerTrackingLogging_Update
AFTER UPDATE
ON CustomerTracking
BEGIN
INSERT INTO CustomerTracking_History (MainID_Old,MainID_New,CustomerName_Old,CustomerName_New,CustomerAddress_Old,CustomerAddress_New,ActionTaken,Modified) VALUES (OLD.MainID,NEW.MainID,OLD.CustomerName,NEW.CustomerName,OLD.CustomerAddress,NEW.CustomerAddress,'Insert or Update',datetime('NOW'));
END

```

  

## Unique ID on Trigger

```
CREATE TRIGGER CustomerTrackingSetMainID
AFTER INSERT
ON CustomerTracking
BEGIN
UPDATE CustomerTracking SET MainID = (Select lower(hex(randomblob(16)))) Where MainID is Null;
END

```