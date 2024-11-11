

# [Task Scheduling](https://www.digikey.com/en/maker/projects/introduction-to-rtos-solution-to-part-3-task-scheduling/8fbb9e0b0eed4279a2dd698f02ce125f) ([Introduction to RTOS )](https://www.digikey.com/en/maker/projects/introduction-to-rtos-solution-to-part-3-task-scheduling/8fbb9e0b0eed4279a2dd698f02ce125f)

  

In microcontrollers, we can also set up independent interrupt service routines (ISRs) that can preempt any of the tasks to execute some code. An ISR is used to handle things like hardware timer overflows, pin state changes, or new communication on a bus.

# ![Ticks in an RTOS running concurrent tasks](https://lh7-rt.googleusercontent.com/docsz/AD_4nXdsnH0CyKJ5xcB_-EDTqicqOS1WiWtvBsRt_hjVDYt7UfSeSTBOfG28lEGVAkKMpT_SVYoHxBcPMhKZGOEUqPoHEgqeBU1wRhipoc9JCBjt0S-VqdGRGxMgqRxHLDxvq0i3lhn3PDdaX2Yf3IVxaLgmK0U?key=knHaqz2i28W2NmyjBPkSyg "Ticks in an RTOS running concurrent tasks")

In FreeRTOS, the default time slice is 1 ms, and a time slice is known as a “tick.” A hardware timer is configured to create an interrupt every 1 ms. 

The ISR for that timer runs the scheduler, which chooses the task to run next.

Tasks with higher priority are chosen over tasks with lower priority. This is known as “preemption.” 

  
  

A hardware interrupt is always considered to have a higher priority than any task running in software. As a result, a hardware ISR can interrupt any task. Because of this, we recommend keeping any ISR code as short as possible to reduce interruptions to the running tasks.

**