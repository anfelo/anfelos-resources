```ts
// The base command class defines the common interface for all
// concrete commands.
abstract class Command {
    protected field app: Application
    protected field editor: Editor
    protected field backup: text

    constructor Command(app: Application, editor: Editor) {
        this.app = app
        this.editor = editor
    }

    // Make a backup of the editor's state.
    method saveBackup() {
        backup = editor.text
    }

    // Restore the editor's state.
    method undo() {
        editor.text = backup
    }

    // The execution method is declared abstract to force all
    // concrete commands to provide their own implementations.
    // The method must return true or false depending on whether
    // the command changes the editor's state.
    abstract method execute()
}

// The concrete commands go here.
class CopyCommand extends Command {
    // The copy command isn't saved to the history since it
    // doesn't change the editor's state.
    method execute() {
        app.clipboard = editor.getSelection()
        return false
    }
}

class CutCommand extends Command {
    // The cut command does change the editor's state, therefore
    // it must be saved to the history. And it'll be saved as
    // long as the method returns true.
    method execute() {
        saveBackup()
        app.clipboard = editor.getSelection()
        editor.deleteSelection()
        return true
    }
}

class PasteCommand extends Command {
    method execute() {
        saveBackup()
        editor.replaceSelection(app.clipboard)
        return true
    }
}

// The undo operation is also a command.
class UndoCommand extends Command {
    method execute() {
        app.undo()
        return false
    }
}


// The global command history is just a stack.
class CommandHistory {
    private field history: array of Command

    // Last in...
    method push(c: Command) {
        // Push the command to the end of the history array.
    }

    // ...first out
    method pop(): Command {
        // Get the most recent command from the history.
    }
}


// The editor class has actual text editing operations. It plays
// the role of a receiver: all commands end up delegating
// execution to the editor's methods.
class Editor {
    field text: string

    method getSelection() {
        // Return selected text.
    }

    method deleteSelection() {
        // Delete selected text.
    }

    method replaceSelection(text) {
        // Insert the clipboard's contents at the current
        // position.
    }
}


// The application class sets up object relations. It acts as a
// sender: when something needs to be done, it creates a command
// object and executes it.
class Application {
    field clipboard: string
    field editors: array of Editors
    field activeEditor: Editor
    field history: CommandHistory

    // The code which assigns commands to UI objects may look
    // like this.
    method createUI() {
        // ...
        copy = function() {
          executeCommand(new CopyCommand(this, activeEditor))
        }
        copyButton.setCommand(copy)
        shortcuts.onKeyPress("Ctrl+C", copy)

        cut = function() {
          executeCommand(new CutCommand(this, activeEditor))
        }
        cutButton.setCommand(cut)
        shortcuts.onKeyPress("Ctrl+X", cut)

        paste = function() {
          executeCommand(new PasteCommand(this, activeEditor))
        }
        pasteButton.setCommand(paste)
        shortcuts.onKeyPress("Ctrl+V", paste)

        undo = function() {
          executeCommand(new UndoCommand(this, activeEditor))
        }
        undoButton.setCommand(undo)
        shortcuts.onKeyPress("Ctrl+Z", undo)
    }

    // Execute a command and check whether it has to be added to
    // the history.
    method executeCommand(command) {
        if (command.execute)
            history.push(command)
    }

    // Take the most recent command from the history and run its
    // undo method. Note that we don't know the class of that
    // command. But we don't have to, since the command knows
    // how to undo its own action.
    method undo() {
        command = history.pop()
        if (command != null)
            command.undo()
    }
}
```