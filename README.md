# Python Video Player
Plays videos in pygame

## Usage
This is indended to be used alongside another project, so it will not render standalone.

To start, create a video player instance:
```py
videoPlayer = VideoPlayer()
```

To start a video, use the play function. Note that the audio will only be able to play if an mp3 is provided (I have not figured out how to read audio from mp4)
```py
videoPlayer.play("vid.mp4", "vid.mp3")
```

The audio will play continuously. To get the current frame surface, use the get_frame command. Pass in delta time (in seconds) since the last get_frame call. I get delta time with `pg.Clock.tick() / 1000.0`:
```py
frame = videoPlayer.get_frame(delta_time)
```
This will return a surface if successful, and none if no frame coule be read.

### Additional Functionality
In addition to the core features, there are a few other helpful functions:
- `videoPlayer.stop()` : Stops the current video
- `videoPlayer.pause()` : Pauses the current video temporarily
- `videoPlayer.unpause()` : Resumes the current video
- `videoPlayer.set_volume(volume: int)` : Sets the audio volume (wrapper for pg.mixer.music.set_volume)

## Example
Here is an example of how it could be used:
```py
import pygame as pg
from video import VideoPlayer


class App:
    def __init__(self) -> None:
        self.win_size = (800, 800)
        self.win = pg.display.set_mode(self.win_size, pg.RESIZABLE)
        self.clock = pg.Clock()

        # Initalize the video player
        self.videoPlayer = VideoPlayer()
    
    def draw(self):
        self.win.fill((0, 0, 0))

        # Get the frame from the video player as a pygame surface
        # Must pass in delta time (in seconds) from the last get_frame call
        frame = self.videoPlayer.get_frame(self.dt)
        # If we got a frame, blit to the screen
        if frame:
            # Adjust scale
            ratio = self.win_size[0] / frame.get_width()
            frame = pg.transform.scale(frame, (frame.get_width() * ratio, frame.get_height() * ratio)) 
            self.win.blit(frame, (0, (self.win_size[1] - frame.get_height())/2))

        pg.display.flip()

    def start(self):
        self.run = True
        while self.run:
            for event in pg.event.get():
                if event.type == pg.QUIT:
                    pg.quit()
                    self.run = False

                if event.type == pg.VIDEORESIZE:
                    self.win_size = (event.w, event.h)
            
                # Commands for the video player
                if event.type == pg.KEYDOWN:
                    if event.key == pg.K_p:
                        self.videoPlayer.play("vid.mp4", "vid.mp3")
                    if event.key == pg.K_s:
                        self.videoPlayer.stop()
                    if event.key == pg.K_q:
                        self.videoPlayer.pause()
                    if event.key == pg.K_e:
                        self.videoPlayer.unpause()

            self.dt = self.clock.tick() / 1000
            self.draw()


vidPlayer = App()
vidPlayer.start()
```
