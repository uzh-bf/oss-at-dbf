---
title: Remotion
---

Remotion is a tool for video creation in react. The principle is simply rendering multiple images after another and thus creating a video. You can render, edit and export videos with just using react. Remotion provides a whole bunch of pre-built components which are ready to be used. Be sure to check out their website and documentation.

# Set Up

## Set Up and Installation

The Set Up is really straight forward if you follow the online guide closely, but be wary of doing the correct one!

### New projects

[https://www.remotion.dev/docs/](https://www.remotion.dev/docs/)

### Existing projects

[https://www.remotion.dev/docs/brownfield](https://www.remotion.dev/docs/brownfield)

Follow the online guide and set up the folder structure as well.

# Usage

You start the preview server with this command:

```plain
npx remotion preview remotion/index.ts
```

You can user in-element styling as well as tailwind ([https://www.remotion.dev/docs/tailwind](https://www.remotion.dev/docs/tailwind))

## Use Remotion in an existing project

[https://www.remotion.dev/docs/brownfield](https://www.remotion.dev/docs/brownfield)

There is a neat guide on how to use remotion in an existing project.

## Use Remotion in a new project

Navigate to the folder where you want to start you new project.

Run the command:

```plain
npm init video
```

You then have to give your video a name.

Choose one of the given templates:

![](https://t4648007.p.clickup-attachments.com/t4648007/86ede85d-98c4-4d51-80a3-ee6f0a0c7150/image.png)

After the project is loaded you can choose to open the project in VS Code.

This sets up a File Structure where Video.tsx file is basically the whole video.

## Preview

To watch the videos (i.e. compositions or a collection of compositions) you have created you can run the command

```plain
npm start
```

in your terminal. This will open a localhost:300X tab in your browser. There you can see all the components and the whole video.

![](https://t4648007.p.clickup-attachments.com/t4648007/00cd0eb9-8ed8-4e30-9912-3d72a0094b44/image.png)

In the top right corner you can also see the file name. You can adjust the speed, skip frames, cut the video, zoom in and many more actions. They are all very self-explanatory.

When running the Video you can see on the right of the screen at the height of the Play button the current frames per second.

## Render

To render the video ([https://www.remotion.dev/docs/render](https://www.remotion.dev/docs/render)) you can simply enter the command:

```plain
npm run build
```

You will see see where the video is saved (usually '/out/video.mp4'). Also be sure to check out the documentation for existing projects ([https://www.remotion.dev/docs/brownfield](https://www.remotion.dev/docs/brownfield)).

# Video Creation / Coding

Be sure to check out this youtube video which explains the basics of remotion pretty good

[https://www.youtube.com/watch?v=deg8bOoziaE&ab_channel=Fireship](https://www.youtube.com/watch?v=deg8bOoziaE&ab_channel=Fireship)

## <Composition>

Compositions ([https://www.remotion.dev/docs/composition](https://www.remotion.dev/docs/composition)) are mainly used in the main class where everything is put together (e.g. Root.tsx, Video.tsx, etc.). Components are built in seperate files and make up the video. You can watch the individual compositions in the video player by pressing on them.

```typescript
<Composition
  id="OnlyLogo"
  component={Logo}
  durationInFrames={180}
  fps={60}
  width={1920}
  height={1080}
/>
```

![](https://t4648007.p.clickup-attachments.com/t4648007/730a7717-1b6e-4305-a96b-cc7f08bfb0e4/image.png)

| id               | helps identify the composition in the video player, it should reference a react component |
| ---------------- | ----------------------------------------------------------------------------------------- |
| component        | a react component which is defined in a different file                                    |
| durationInFrames | how many frames the video has                                                             |
| fps              | frames per second (speed of the video)                                                    |
| width            | width of the component                                                                    |
| height           | height of the component                                                                   |

The **duration** of the video is durationInFrames / fps (here 180 / 60 = 3 sec).

## Transformations

You can transform your elements in various ways such as the opacity, the size, the placement and the shear. You can for example change the size of objects inside your video or change their opacity. Lets say you would like to implement a progress bar. Then you can change start by creating a single column, move it to the right and increase the size of it over time. You could also use many different bars. Then you could change their opacity and color over time.

Check out the documentation ([https://www.remotion.dev/docs/transforms](https://www.remotion.dev/docs/transforms)) to learn more.

## Animating Properties

With the help of useCurrentFrame(), transformations and possibly useVideoConfig() you can create animations of your elements ([https://www.remotion.dev/docs/animating-properties](https://www.remotion.dev/docs/animating-properties)). You can even use spring animations (also a prebuilt feature of Remotion):

![](https://t4648007.p.clickup-attachments.com/t4648007/fa707667-c769-40ba-87a5-286c5e409196/image.png)

![](https://t4648007.p.clickup-attachments.com/t4648007/da779c9c-e42b-4ab0-87a3-41fb282fd3bd/image.png)

![](https://t4648007.p.clickup-attachments.com/t4648007/d85638e2-ef95-441b-b964-944599bbb9ed/image.png)

![](https://t4648007.p.clickup-attachments.com/t4648007/f0bdfedd-feb7-4039-8d4b-04914e66e0e5/image.png)

## Styling

You can style components with inline styles inside the div.

```typescript
style={{
  opacity: op1,
  transform: `translateX(80px) rotate(180deg)`
}}
```

# API

Here are some useful features of the API described. There are tons more however which you can check out on their website: [https://www.remotion.dev/docs/api](https://www.remotion.dev/docs/api)

## useVideoConfig()

You can use this API ([https://www.remotion.dev/docs/use-video-config](https://www.remotion.dev/docs/use-video-config)) to get useful information (fps, durationInFrames, width, length) about the video you are working on. You have to import useVideoConfig from 'remotion.

Then specify which attributes you want to get:

```typescript
const { fps, durationInFrames, width, height } = useVideoConfig()
```

and finally you can use those attributes:

```typescript
<div>
  The video has the resolution: {width}x{heigh} <br/>
  and the video is {durationInFrames / fps} seconds long.
</div>
```

## useCurrentFrame()

More useful than useVideoConfig(), useCurrentFrame() ([https://www.remotion.dev/docs/use-current-frame](https://www.remotion.dev/docs/use-current-frame)) will give you the current frame of the video. With the help of this information you can then do animations like fading or movement.

```typescript
const opacity = useCurrentFrame() / durationInFrames
```

Here the constant opacity is used to create a fading animation. This specific animation results in the component being slowly rendered until it is fully visible at the end of the video. Another example is to fade an element to full opacity at a specific time (i.e. frame):

```typescript
const opacity = Math.min(1, useCurrentFrame() / 60)
```

To use this animation you can simply put the constant opacity in the in-line styling:

```typescript
style={{
  justifyContent: "center",
  alignItems: "center",
  opacity
}}>
```

In the 'Putting it all together' section a short demo is shown where some key tools of Remotion are used to demonstrate some features, there useCurrentFrame() plays an integral part.

## interpolate()

This is a very useful function which helps calculate the 'in-between' of an animation where you know the beginning and the end.

![](https://t4648007.p.clickup-attachments.com/t4648007/4f790262-fecf-4df1-9f7f-95747f9aba2c/image.png)

```typescript
const frame = useCurrentFrame()
const opacity = interpolate(frame, [0, 60], [0, 1], { extrapolateRight: "clamp" })
```

The Result of this function is that the component which uses opacity in its style-props will go from an opacity of 0 to 1 over 60 frames and stay at that opacity.

This tutorial explains the interpolate() function in more details.

[https://www.youtube.com/watch?v=sff_CdWw\_-c&ab_channel=JonnyBurger](https://www.youtube.com/watch?v=sff_CdWw_-c&ab_channel=JonnyBurger)

## Shapes

Remotion offers six different prebuilt shapes such as a rectangle, an ellipse or a circle. Those shapes can be implemented inside your components ([https://www.remotion.dev/docs/shapes](https://www.remotion.dev/docs/shapes/)).

# Summary

Remotion offers a variety of different tools to help you create programmatic videos.

The following list should help to give an overview over what is needed to implement a video from beginning to end:

- [ ]     Install Remotion
- [ ]     Create a new project or integrate it in an existing one
- [ ]     Start the preview
- [ ]     Code the video with the different components (shapes, compositions, interpolations, etc.)
- [ ]     Render the video if needed

# Further References

## Documentation

- New project: [https://www.remotion.dev/docs](https://www.remotion.dev/docs/)
- Existing project: [https://www.remotion.dev/docs/brownfield](https://www.remotion.dev/docs/brownfield)
- Tailwind: [https://www.remotion.dev/docs/tailwind](https://www.remotion.dev/docs/tailwind)
- Render: [https://www.remotion.dev/docs/render](https://www.remotion.dev/docs/render)
- Composition: [https://www.remotion.dev/docs/composition](https://www.remotion.dev/docs/composition)
- Transformations: [https://www.remotion.dev/docs/transforms](https://www.remotion.dev/docs/transforms)
- Animating Properties: [https://www.remotion.dev/docs/animating-properties](https://www.remotion.dev/docs/animating-properties)
- API: [https://www.remotion.dev/docs/api](https://www.remotion.dev/docs/api)
- useVideoConfig(): [https://www.remotion.dev/docs/use-video-config](https://www.remotion.dev/docs/use-video-config)
- useCurrentFrame(): [https://www.remotion.dev/docs/use-current-frame](https://www.remotion.dev/docs/use-current-frame)
- Shapes: [https://www.remotion.dev/docs/shapes](https://www.remotion.dev/docs/shapes/)

## Tutorials

- Fireship: [https://www.youtube.com/watch?v=deg8bOoziaE&ab_channel=Fireship](https://www.youtube.com/watch?v=deg8bOoziaE&ab_channel=Fireship)
- interpolate(): [https://www.youtube.com/watch?v=sff_CdWw\_-c&ab_channel=JonnyBurger](https://www.youtube.com/watch?v=sff_CdWw_-c&ab_channel=JonnyBurger)
- Soft Introduction:

[https://www.youtube.com/watch?v=szh2Qgo9SVE&ab_channel=uidotdev](https://www.youtube.com/watch?v=szh2Qgo9SVE&ab_channel=uidotdev)

- Extended Tutorial:

[https://www.youtube.com/watch?v=VOX98RoITMk&ab_channel=CoderOne](https://www.youtube.com/watch?v=VOX98RoITMk&ab_channel=CoderOne)
