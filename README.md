# tsq
Tasks as a Stack of Queues: a _lua script_ implementation

Just an idea I have been wanting to try out for awhile

Its a sort of command line task manager that allows you to push new tasks to a stacklike thing. Once youre done you can pop it off to go to the next task

eg:
  tsq p go to store -- this is what you start
  tsq p fix car -- this is an additional task that you started after that one
  tsq p find a wrench -- another one
  tsq o  -- current task is now fix car
  tsq e call such and such -- this will be the next task once you pop the current one

You can also: defer (enqueue current one stack position down), reserve (save in a hidden queue for later), undo and redo, cycle the queue

~~I literally don't know if it is any use but I made it sooo..~~

Its actually pretty useful

There are variables that you can set using the command tsq set varname value, currently the variables are: undoMax or something like that I forgot what I called it. Open up the file and check the variable name if you care to change it

```
tsq: Tasks as a Stack of Queues:
  current / c            print the current task
                         A
                         C B   <- current
  push / p               push a new task onto the stack and make it current
                         A
                         C B
                         N     <- pushed
  enqueue / e            queue a new task, these will be started after popping the current task
                         A
                         C B N <- queued
  pop / o                remove the current task from the queue and proceed to the latest enqueued task, or last pushed
                         A     
                         B     <- popped
                           and:
                         A     <- popped again
  defer / d              move the current task down the stack and enqueue it
                         A C   <- defered
                         B
  cycle / y              move the current task to the end of the current queue
                         A
                         B C   <- cycled
  count / n              gives the current total number of tasks
  stack-count / sc       gives the current size of the stack
  queue-count / qc       gives the current size of the queue at the top of the stack
  stack / s              prints the first task in each queue of the stack
  queue / q              prints the current queue
  tree / t               prints the whole stack-queue tree sorta like below
  clear                  clears the entire stack
  reserve / r            sets the current task aside in a separate stack
  push-reserved / pr     pushes a reserved task onto the stack
  enqueue-reserved / er  enqueues a reserved task onto the current queue
  clear-reserved / cr    clears all reserved tasks
  value / v              print internal variable values
  set / =                set internal variable values
  -------------
  A B C          <- queue
  D E F G H     ^
  I             |   stack
  J K           v
                    Order: J K I D E F G H A B C
```

How to install:
* Put it into your ~/bin/ directory or wherever tf you want
* chmod +x tsq ~~.lua~~
* ~~Optionally remove the .lua extension to make it quicker to type~~
* You have to have lua-cjson installed so you also have to do:
* * sudo luarocks install lua-cjson
I guess you could somehow get the lua-cjson file from luarocks and install it manually but good luck I'm not sure how to do that

Oh, bytw the program in its default state creates a file ~/.tsq.json so if you want that somewhere else you can change that by opening the file up and changing it

Here is a little (tclsh) script you can use to make a zenity popup window to push a new task
```
#!/usr/bin/tclsh
set res ""
catch {set res [exec zenity --entry --width 400 --title "tsq push" --text [exec tsq c]]}
if {$res != ""} {exec tsq p $res}
```

If you use xbindkeys then you can add something like
```
# zenity current
"exec zenity --info --width 400 --title "tsq current" --text "$(tsq c)""
  alt + c
```
To your ~/.xbindkeysrc to make a hotkey for this

