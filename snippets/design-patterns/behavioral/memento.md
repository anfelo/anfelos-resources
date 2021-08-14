```ts
// The originator holds some important data that may change over
// time. It also defines a method for saving its state inside a
// memento and another method for restoring the state from it.
class Editor {
    private field text, curX, curY, selectionWidth

    method setText(text) {
        this.text = text
    }

    method setCursor(x, y) {
        this.curX = x
        this.curY = y
    }

    method setSelectionWidth(width) {
        this.selectionWidth = width
    }

    // Saves the current state inside a memento.
    method createSnapshot():Snapshot {
        // Memento is an immutable object; that's why the
        // originator passes its state to the memento's
        // constructor parameters.
        return new Snapshot(this, text, curX, curY, selectionWidth)
    }

// The memento class stores the past state of the editor.
class Snapshot {
    private field editor: Editor
    private field text, curX, curY, selectionWidth

    constructor Snapshot(editor, text, curX, curY, selectionWidth) {
        this.editor = editor
        this.text = text
        this.curX = x
        this.curY = y
        this.selectionWidth = selectionWidth
    }

    // At some point, a previous state of the editor can be
    // restored using a memento object.
    method restore() {
        editor.setText(text)
        editor.setCursor(curX, curY)
        editor.setSelectionWidth(selectionWidth)
    }
}

// A command object can act as a caretaker. In that case, the
// command gets a memento just before it changes the
// originator's state. When undo is requested, it restores the
// originator's state from a memento.
class Command {
    private field backup: Snapshot

    method makeBackup() {
        backup = editor.createSnapshot()
    }

    method undo() {
        if (backup != null)
            backup.restore()
    // ...
    }
}
```