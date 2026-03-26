# IMGD/CS 4300: Graphics, Simulation, and Aesthetics

Instructor: [Charlie Roberts](https://charlie-roberts.com)  
PLA: Cas Cherniko  
Time/Place: T-F 2-3:50 PM, Fuller Labs 222

This course explores digital simulations and strategies for interactive representation. Topics include:

- Parallel computing on graphics processing units (GPUs)  
- Realtime graphics techniques  
- The history of analog visuals (visual music, liquid projection, video synthesis etc.)  
- Interaction techniques for controlling digital simulations  

Students will examine and create digital simulations of real world phenonmenon, such as chemical diffusion models, models of artifical life, fluid simulations, and video feedback. The class is designed to encourage both technical and aesthetic exploration.  

All students will be expected to maintain a course website that contains links to all assignments. Videos should be posted to "permanent" locations (Vimeo, YouTube, bonus street cred for PeerTube) and linked in your course website. Details on this website can be found in the [onboarding assignment](./A1.onboarding.md). 

Assignments and examples for this class will primarily use [WebGPU](https://cohost.org/mcc/post/1406157-i-want-to-talk-about-webgpu) / JavaScript, however, other environments and systems for GPU programming will be discussed and may be used for final projects; just talk to me about it first!

## Office hours
I will spend time on Discord answering questions asynchronously. Whenever possible, please post questions *publicly* in Discord so everyone can learn from the answers... and there's a good chance someone else in the class might be able to help with technical questions as well. My in-person office hours are from 2--4 on Wednesday, in Fuller Labs B20... please stop by! Some good reasons to stop by in-person office hours include:
- We can't debug your problems over Discord
- Brainstorming on final project / assignment ideas
- You want to talk about art / music / graphics / programming more generally
- You're interested in learning more about [my research](http://charlie-roberts.com)
- You want to tell me about your research
- You'd like to sit and work a bit with me/other students nearby in case questions popup
- Anything else, really!

Cas, the PLA for the course, has office hours as follows:
- Monday: 4-6PM, remote, in the Discord server voice channel
- T-F: 11-1 PM, in person in Fuller Labs A22

Cas has previously taken the course, has lots of additional graphics/shader experience to draw on, and can also help troubleshoot WebGPU+Linux compatability issues. They will also be helping answer questions in the Discord server aynchronously.

## Course Outline
A best guess is provided for how much time should be spent on each assignment. Some might take less time, some might take a bit more depending on how invested you become in the individual assignment. This schedule will be updated / changed on a weekly basis; consider it a rough outline of the course (especially at the start of the course!), not a contract.

### Week 1: Getting Started / GLSL Live Coding / Visual Music
- *Basic Intro to WGSL / Shader programmming* Assignment:  
    - Complete the [onboarding](./A1.onboarding.md). Due 3/24 (2 hours).
- *Shader Live Coding and Visual Music*. Assignments:  
    - Read / experiment with [The Book of Shaders](http://thebookofshaders.com) up through the lesson on [Noise](https://thebookofshaders.com/11/). Due 3/24 (4 hours).
    - Complete the Shader Live Coding Assignment, due 3/24 (6 hours).  

### Week 2: Using WebGPU and Video synthesis history
- *Readings*
    - Read the final two chapters of [The Book of Shaders](http://thebookofshaders.com). Due 3/27 (2 hours).
- *Intro to using WebGPU*:
    - Complete the [WebGPU Intro Assignment](/A3.webgpu_intro.md), due 4/3 by start of class, (6 hours).
  
### Week 3: Automata and Morphogenesis

### Week 4: Vertex Shaders and Particle Effects
   
### Week 5: Agent-based Simulations

### Week 6: Interaction in the Digital Arts + Other ways to tame the GPU: CUDA / Game Engines

### Week 7: Final Project Presentations &amp; Wrapup  

## Grades
Your course grade comes from three parts:

Assignments (55%)  
Final Project (35%)  
In-class assignments + attendance quizzes (10%)  

There will most likely be 4–5 assignments in the course in addition to the final project. I don't want anyone getting too far behind in the class as the technical material is sequential, but I'll do my best to help you figure out a reasonable schedule for completing your coursework if you fall behind. Get everything turned in!

## Attendance
Attendance is required. That said, life happens... please notify me in advance over Discord if you must absoluately must miss class so we can figure out how best to handle it.

## Academic Integrity
The goal of this class is to both create aesthetically interesting content and understand the code used to create it. In order to understand the code, you need to author it yourself. Copying and pasting signficant portions of assigned algorithms is not allowed in this class, unless explicitly stated by the instructor. If you have a question about this, please ask me.

Collaboration is encouraged in this class. There are many ways in which you can assist others without giving them code and answers. Providing low-level implemetation details (small code fragments that contribute to, but don't complete on their own, major portions of assignments) is great. For example, telling a classmate that they need to use the `smoothstep()` function in GLSL and showing them how to call it.

### On LLMs
The use of LLMs for significant code generation is not allowed, and any minor use of LLMs must be disclosed in the README of individual assignments. This class is an opportunity to learn the low-level details of shader programming, please don't use shortcuts that don't involve research / collaboration.
