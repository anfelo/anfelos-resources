```ts
// The AudioPlayer class acts as a context. It also maintains a
// reference to an instance of one of the state classes that
// represents the current state of the audio player.
class AudioPlayer {
    field state: State
    field UI, volume, playlist, currentSong

    constructor AudioPlayer() {
        this.state = new ReadyState(this)

        // Context delegates handling user input to a state
        // object. Naturally, the outcome depends on what state
        // is currently active, since each state can handle the
        // input differently.
        UI = new UserInterface()
        UI.lockButton.onClick(this.clickLock)
        UI.playButton.onClick(this.clickPlay)
        UI.nextButton.onClick(this.clickNext)
        UI.prevButton.onClick(this.clickPrevious)
    }

    // Other objects must be able to switch the audio player's
    // active state.
    method changeState(state: State) {
        this.state = state
    }

    // UI methods delegate execution to the active state.
    method clickLock() {
        state.clickLock()
    }
    method clickPlay() {
        state.clickPlay()
    }
    method clickNext() {
        state.clickNext()
    }
    method clickPrevious() {
        state.clickPrevious()
    }

    // A state may call some service methods on the context.
    method startPlayback() {
        // ...
    }
    method stopPlayback() {
        // ...
    }
    method nextSong() {
        // ...
    }
    method previousSong() {
        // ...
    }
    method fastForward(time) {
        // ...
    }
    method rewind(time) {
        // ...
    }
}


// The base state class declares methods that all concrete
// states should implement and also provides a backreference to
// the context object associated with the state. States can use
// the backreference to transition the context to another state.
abstract class State {
    protected field player: AudioPlayer

    // Context passes itself through the state constructor. This
    // may help a state fetch some useful context data if it's
    // needed.
    constructor State(player) {
        this.player = player
    }

    abstract method clickLock()
    abstract method clickPlay()
    abstract method clickNext()
    abstract method clickPrevious()
}


// Concrete states implement various behaviors associated with a
// state of the context.
class LockedState extends State {

    // When you unlock a locked player, it may assume one of two
    // states.
    method clickLock() {
        if (player.playing)
            player.changeState(new PlayingState(player))
        else
            player.changeState(new ReadyState(player))
    }

    method clickPlay() {
        // Locked, so do nothing.
    }

    method clickNext() {
        // Locked, so do nothing.
    }

    method clickPrevious() {
        // Locked, so do nothing.
    }
}


// They can also trigger state transitions in the context.
class ReadyState extends State {
    method clickLock() {
        player.changeState(new LockedState(player))
    }

    method clickPlay() {
        player.startPlayback()
        player.changeState(new PlayingState(player))
    }

    method clickNext() {
        player.nextSong()
    }

    method clickPrevious() {
        player.previousSong()
    }
}


class PlayingState extends State {
    method clickLock() {
        player.changeState(new LockedState(player))
    }

    method clickPlay() {
        player.stopPlayback()
        player.changeState(new ReadyState(player))
    }

    method clickNext() {
        if (event.doubleclick)
            player.nextSong()
        else
            player.fastForward(5)
    }

    method clickPrevious() {
        if (event.doubleclick)
            player.previous()
        else
            player.rewind(5)
    }
}
```