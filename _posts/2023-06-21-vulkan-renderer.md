---
layout: post
title:  "Ard Renderer"
summary: "A high performance realtime Vulkan renderer built with modern techniques. Written in Rust."
date:   2022-10-09 15:39:40
preview: /assets/renderer_thumbnail.png
youtubeId: iEUc-SF0Lcw
---

![Picture 1](/assets/renderer_header.png)

## Overview

The renderer of [my engine](https://github.com/ReeCocho/ard-engine) is designed with bindless rendering techniques and GPU driven rendering. Some of the features I implemented include:

* **Unified vertex memory** using a custom allocator for mesh data.
* **GPU driven rendering** using compute shaders to generate draw calls and perform frustum and **hierarchical Z-Buffer occlusion culling**.
* **Bindless textures and materials** using texture arrays and SSBOs.
* Mesh and mipmap **texture streaming**.
* **PBR rendering** with **image based lighting**, **clustered shading**, and **cascaded shadow maps**.
* Image effects like **SSAO**, **FXAA**, and **adaptive luminance**.

You can find [a playable demo here](https://drive.google.com/file/d/1A1e2JNk10fu3KEoe7OPyRiK1NRtZHqud/view?usp=sharing) which contains the Bistro scene.

Below is a video demonstrating the renderer using the Amazon Lumberyard Bistro scene with all rendering features enabled.

{% include youtubePlayer.html id=page.youtubeId %}

## Design Goals

I knew from the start I wanted to implement modern rendering techniques like GPU driven and bindless rendering techniques. These techniques improve performance by decreasing the amount of communication that needs to occur between the CPU and GPU. I was inspired by two articles: one by [Adrian Courrèges](https://www.adriancourreges.com/blog/2016/09/09/doom-2016-graphics-study/) and another by [Simon Coenen](https://simoncoenen.com/blog/programming/graphics/DoomEternalStudy.html). They are both excellent analyses of the rendering techniques used by Id Software's DOOM (2016) and DOOM Eternal respectively.

## Implementation

My initial implementation of the renderer was written purely in Vulkan, but turned out to be extremely hard to maintain. There were a few pain points with Vulkan that led me to consider a redesign. In order of importance:

* Synchronization is difficult to get right and, when done incorrectly, can lead to problems that are hard to debug.
* Image layout transitions and pipeline barriers are similarly tedious.
* The raw Vulkan API does not support RAII.
* Render passes are objects, and pipelines require access to them during creation.

To solve these problems I designed a hardware abstraction layer built on top of Vulkan, which I named PAL. Synchronization turned out to be the most challenging aspect of the design because there is no one-size-fits-all solution for every application. I knew this going in, so I had to analyze my use case and determine what trade offs I was willing to make to achieve my primary goal of having no manual synchronization.

The redesign was a huge step in the right direction. The code base for the renderer is much smaller and less complicated than it used to be, while also having more features than the previous design.

A small example of the hardware abstraction layer can be seen here:

```rust
...

// This code comes from an example of PAL on the engine's GitHub page. It is run in a render loop.

// This write is being performed on the CPU and synchronization with the GPU is automatically performed.
uniform_buffer
    .write(0)
    .unwrap()
    .copy_from_slice(bytemuck::cast_slice(&[UniformData {
        offset: [timer.cos() * 0.1, timer.sin() * 0.1],
    }]));

// Render passes are self contained, meaning you can't do things like perform draw calls outside of a render pass, 
// making the API harder to misuse.
command_buffer.render_pass(
    // Render passes in PAL are not objects which is unlike Vulkan. Instead, you define render passes at the location 
    // they are used. Internally, VkRenderPass objects are cached.
    RenderPassDescriptor {
        // Image layout transitions are handled automatically. No pipeline barrier neccessary!
        color_attachments: vec![ColorAttachment {
            source: ColorAttachmentSource::SurfaceImage(&surface_image),
            load_op: LoadOp::Clear(ClearColor::RgbaF32(0.0, 0.0, 0.0, 0.0)),
            store_op: StoreOp::Store,
        }],
        depth_stencil_attachment: None,
    },
    |pass| {
        // Pipelines can be used in any render pass as long as they have compatible attachments.
        pass.bind_pipeline(pipeline.clone());

        pass.bind_sets(0, vec![&set]);

        // These buffers were written to on a transfer queue. Appropriate semaphores are used for synchronization 
        // on submit.
        pass.bind_vertex_buffers(
            0,
            vec![VertexBind {
                buffer: &vertex_buffer,
                array_element: 0,
                offset: 0,
            }],
        );
        pass.bind_index_buffer(&index_buffer, 0, 0, IndexType::U16);

        pass.draw_indexed(3, 1, 0, 0, 0);
    },
);

// All resources and certain operations can be given debug names which show up in programs like RenderDoc.
context.main().submit(Some("main_pass"), command_buffer);

...

// At the end of execution, all resources are cleaned up using RAII
```

## Room For Improvement

There are certainly things I look forward to implementing in the future to improve the quality of this piece of my engine.

* PAL is still in a "pre-release" state and is missing features like support for multithreaded command recording.
* Static geometry frustum culling could be accelerated using something like a BVH.
* The renderer is missing certain features such as transparent objects, point and spot light shadows, and certain image effects like bloom.
* I'm very interested in using the Vulkan ray tracing API to do realtime global illumination.
