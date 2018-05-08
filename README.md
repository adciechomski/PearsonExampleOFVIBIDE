--------------- class: CVBECommandHandler
```
Option Explicit

Public WithEvents EvtHandler As VBIDE.CommandBarEvents

Private Sub EvtHandler_Click(ByVal CommandBarControl As Object, _
    Handled As Boolean, CancelDefault As Boolean)

    '''''''''''''''''''''''''''''''''''''''''''''''''''''
    ' This is called when a item is clicked. Call the
    ' procedure named in the OnAction property of the
    ' CommandBarControl passed into this procedure.
    '''''''''''''''''''''''''''''''''''''''''''''''''''''
    
    On Error Resume Next
    Application.Run CommandBarControl.OnAction
    '''''''''''''''''''''''''''''''''''''''''''''''''''''
    ' Tell VBIDE that we've handled the event.
    '''''''''''''''''''''''''''''''''''''''''''''''''''''
    Handled = True
    CancelDefault = True
End Sub


----module modTestCode:

Option Explicit

Public Sub Procedure_One()
    MsgBox "Procedure One"
End Sub

Public Sub Procedure_Two()
    MsgBox "Procedure Two"
End Sub

Public Sub Auto_Open()
    AddNewVBEControls
End Sub

Public Sub Auto_Close()
    DeleteMenuItems
End Sub




------module1

Option Explicit

Private MenuEvent As CVBECommandHandler
Private CmdBarItem As CommandBarControl
Public EventHandlers As New Collection

Private Const C_TAG = "MY_VBE_TAG"

Sub AddNewVBEControls()

Dim Ctrl As Office.CommandBarControl

'''''''''''''''''''''''''''''''''''''''''''''''''
' Delete any existing controls with our Tag.
'''''''''''''''''''''''''''''''''''''''''''''''''
Set Ctrl = Application.VBE.CommandBars.FindControl(Tag:=C_TAG)
Do Until Ctrl Is Nothing
    Ctrl.Delete
    Set Ctrl = Application.VBE.CommandBars.FindControl(Tag:=C_TAG)
Loop

'''''''''''''''''''''''''''''''''''''''''''''''''
' Delete any existing event handlers.
'''''''''''''''''''''''''''''''''''''''''''''''''
Do Until EventHandlers.Count = 0
    EventHandlers.Remove 1
Loop

'''''''''''''''''''''''''''''''''''''''''''''''''
' add the first control to the Tools menu.
'''''''''''''''''''''''''''''''''''''''''''''''''
Set MenuEvent = New CVBECommandHandler
With Application.VBE.CommandBars("Menu Bar").Controls("Tools")
    Set CmdBarItem = .Controls.Add
End With
With CmdBarItem
    .Caption = "First Item"
    .BeginGroup = True
    .OnAction = "'" & ThisWorkbook.Name & "'!Procedure_One"
    .Tag = C_TAG
End With

Set MenuEvent.EvtHandler = Application.VBE.Events.CommandBarEvents(CmdBarItem)
EventHandlers.Add MenuEvent

'''''''''''''''''''''''''''''''''''''''''''''''''
' add the second control to the Tools menu.
'''''''''''''''''''''''''''''''''''''''''''''''''
Set MenuEvent = New CVBECommandHandler
With Application.VBE.CommandBars("Menu Bar").Controls("Tools")
    Set CmdBarItem = .Controls.Add
End With
With CmdBarItem
    .Caption = "Second Item"
    .BeginGroup = False
    .OnAction = "'" & ThisWorkbook.Name & "'!Procedure_Two"
    .Tag = C_TAG
End With

Set MenuEvent.EvtHandler = Application.VBE.Events.CommandBarEvents(CmdBarItem)
EventHandlers.Add MenuEvent

End Sub

Sub DeleteMenuItems()
'''''''''''''''''''''''''''''''''''''''''''''''''''''
' This procedure deletes all controls that have a
' tag of C_TAG.
'''''''''''''''''''''''''''''''''''''''''''''''''''''
    Dim Ctrl As Office.CommandBarControl
    Set Ctrl = Application.VBE.CommandBars.FindControl(Tag:=C_TAG)
    Do Until Ctrl Is Nothing
        Ctrl.Delete
        Set Ctrl = Application.VBE.CommandBars.FindControl(Tag:=C_TAG)
    Loop
End Sub
```
