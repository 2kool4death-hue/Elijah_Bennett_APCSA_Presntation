# CMU CS Academy - Task Manager & Stress Tracker

```python
app.background = rgb(26, 26, 26)

# --- UI Setup ---
# Title
Label('Task Manager & Stress Tracker', 200, 30, fill='white', size=20, bold=True)

# Stress Tracker UI elements
Label('Current Stress Level:', 200, 75, fill='white', size=14)

# Background of the stress bar (border/container)
Rect(100, 95, 200, 20, fill=None, border='white', borderWidth=1)

# The dynamic stress bar
stressBar = Rect(101, 96, 198, 18, fill=rgb(255, 100, 100))

# Percentage text label
stressLabel = Label('100% Load', 200, 130, fill='white', size=14, bold=True)

# Dynamic Encouragement/Stress Quote Sub-label
feedbackLabel = Label('Take it one task at a time!', 200, 155, fill='gray', size=12, italic=True)

# Manual Reset Button
resetButton = Rect(130, 360, 140, 35, fill=None, border='white', borderWidth=1)
resetLabel = Label('Reset Day', 200, 377, fill='white', size=14)


# --- Task Data & Visual Checklist ---
# Each task item stores: [checkbox_box, checkmark_label, text_label, strikethrough_line, is_completed_boolean, click_hitbox]
tasks = [
    [
        Rect(60, 180, 20, 20, fill=None, border='white', borderWidth=2), 
        Label('✓', 70, 190, fill='white', size=16, bold=True, visible=False),
        Label('Complete History Essay', 100, 190, fill='white', size=14, align='left'), 
        Line(100, 190, 250, 190, fill='gray', visible=False, lineWidth=2),
        False,
        Rect(60, 180, 280, 20, fill='white', opacity=0) # Hidden click catcher
    ],
    [
        Rect(60, 220, 20, 20, fill=None, border='white', borderWidth=2), 
        Label('✓', 70, 230, fill='white', size=16, bold=True, visible=False),
        Label('Finish Precalc WebAssign', 100, 230, fill='white', size=14, align='left'), 
        Line(100, 230, 265, 230, fill='gray', visible=False, lineWidth=2),
        False,
        Rect(60, 220, 280, 20, fill='white', opacity=0) 
    ],
    [
        Rect(60, 260, 20, 20, fill=None, border='white', borderWidth=2), 
        Label('✓', 70, 270, fill='white', size=16, bold=True, visible=False),
        Label('Vacuum Living Room', 100, 270, fill='white', size=14, align='left'), 
        Line(100, 270, 240, 270, fill='gray', visible=False, lineWidth=2),
        False,
        Rect(60, 260, 280, 20, fill='white', opacity=0) 
    ],
    [
        Rect(60, 300, 20, 20, fill=None, border='white', borderWidth=2), 
        Label('✓', 70, 310, fill='white', size=16, bold=True, visible=False),
        Label('AP CS Principles Create Task', 100, 310, fill='white', size=14, align='left'), 
        Line(100, 310, 295, 310, fill='gray', visible=False, lineWidth=2),
        False,
        Rect(60, 300, 280, 20, fill='white', opacity=0) 
    ]
]

# FIX LAYER CODES: Move the hitboxes to the back so the checkbox and checkmarks are drawn ON TOP of them
for task in tasks:
    task[5].toBack()


# --- Core Logic Functions ---

def setTaskState(task, isCompleted):
    """Helper function to cleanly handle the visual toggling of any task row."""
    box, checkmark, label, strikeLine, _, _ = task
    task[4] = isCompleted
    
    if isCompleted:
        box.fill = rgb(139, 195, 74)   # Turn checkbox green
        checkmark.visible = True        # Turn checkmark visible
        label.fill = 'gray'             
        strikeLine.visible = True       
    else:
        box.fill = None                 # Clear checkbox
        checkmark.visible = False       # Hide checkmark
        label.fill = 'white'            
        strikeLine.visible = False      


def resetAllTasks():
    # Prompt the user to enter names for all task slots sequentially
    for i in range(len(tasks)):
        taskNum = i + 1
        newTaskName = app.getTextInput(f"Enter a name for Task #{taskNum}:")
        
        if newTaskName in (None, ""):
            newTaskName = f"Fresh Task #{taskNum}"
            
        taskLabel = tasks[i][2]
        taskLine = tasks[i][3]
        taskLabel.value = newTaskName
        
        taskLine.x2 = taskLabel.left + taskLabel.width

    for task in tasks:
        setTaskState(task, False)
        
    updateStressMeter()


def updateStressMeter():
    completedCount = sum(1 for task in tasks if task[4])
    totalTasks = len(tasks)
    
    remainingStressRatio = 1 - (completedCount / totalTasks)
    
    if remainingStressRatio == 0:
        stressBar.visible = False
    else:
        stressBar.visible = True
        stressBar.width = 198 * remainingStressRatio
    
    percentage = int(remainingStressRatio * 100)
    stressLabel.value = "0% - Relaxed!" if percentage == 0 else f"{percentage}% Load"
    
    # Update colors and dynamic feedback text based on percentage
    if percentage > 60:
        stressBar.fill = rgb(255, 100, 100) # Red
        feedbackLabel.value = "Deep breaths. Take a break if needed!"
        feedbackLabel.fill = rgb(255, 100, 100)
    elif percentage > 25:
        stressBar.fill = rgb(251, 192, 45)  # Yellow
        feedbackLabel.value = "Making steady progress!"
        feedbackLabel.fill = rgb(251, 192, 45)
    else:
        stressBar.fill = rgb(139, 195, 74)  # Green
        feedbackLabel.value = "Almost home free!"
        feedbackLabel.fill = rgb(139, 195, 74)
        
    if percentage == 0:
        feedbackLabel.value = "All clear! Enjoy your day."
        feedbackLabel.fill = rgb(139, 195, 74)
        
    return completedCount == totalTasks


def onMousePress(mouseX, mouseY):
    # 1. Check if the manual 'Reset Day' button was pressed
    if resetButton.hits(mouseX, mouseY):
        resetAllTasks()
        return

    # 2. Check regular task rows interaction
    for task in tasks:
        hitbox = task[5]
        
        if hitbox.hits(mouseX, mouseY):
            newState = not task[4]
            setTaskState(task, newState)
                
            allTasksDone = updateStressMeter()
            
            if allTasksDone:
                resetAllTasks()
            break 


def onMouseMove(mouseX, mouseY):
    # Interactive Button Hover State Animation
    if resetButton.hits(mouseX, mouseY):
        resetButton.fill = rgb(50, 50, 50)
        resetLabel.fill = 'yellow'
    else:
        resetButton.fill = None
        resetLabel.fill = 'white'
