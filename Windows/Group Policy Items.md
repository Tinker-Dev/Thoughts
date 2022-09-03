# Group Policy Items

## Remove GPO Driven Taskbar Icons

Depending on how things are being pushed there are a couple of ways this can be tackled.  

  

### Option 1:

Step 1 - Visit the following path on your machine:  

```
%LOCALAPPDATA%\Microsoft\Windows\Shell\LayoutModification.xml

```

  

Step 2 - Remove all item listed between "TaskListPinList" tags.  

```
  </DefaultLayoutOverride>
    <CustomTaskbarLayoutCollection PinListPlacement="Replace">
    <defaultlayout:TaskbarLayout>
      <taskbar:TaskbarPinList>
      <!-- items were here - removed -->
      </taskbar:TaskbarPinList>
    </defaultlayout:TaskbarLayout>
  </CustomTaskbarLayoutCollection>

```

  

Step 3 - Logout and then Log back in.