**Transcript**:

-: All right, so slow applications, network latency,

packet loss, TCP window issues.

And the last one, application connectivity problems.

So when things just go boom

and the application does not connect or drops.

So I'm about to show you that in our final packet capture.

So this was an example

where the client was just having spotty,

intermittent connectivity to the internet.

That's basically, it was just it.

They were trying to go out and access a bunch

of different sites.

Sometimes it worked and sometimes it didn't.

They'd have a bunch of tabs that worked just fine,

open up a bunch of new tabs, nothing was working.

So if we take a look here,

the first thing I notice when I just do just a run down

my packet captures, things like this.

I call this striping.

So I see this when I have a bunch of new connections

that are being attempted,

and then I see resets immediately after.

Now the interesting thing here is that 443,

before that period of loss,

or before this period of striping rather,

is working just fine.

And then after I see 443 working just fine.

So what's the deal?

Why are these connections not getting through?

And if I take a look and I just filter

tcp.flags.reset equals equals one.

If I filter on the resets, you see

that I got a bunch of resets coming from different places.

So I ask myself some questions here.

So, hmm, this client is going out to a bunch

of different places and some are working, some are not.

But here's my resets coming back for some SYNs.

So what are the odds that all

of these different remote websites out here all got together

and said, hey, let's just reset, Chris, all at once?

That's pretty unlikely, especially when in context,

if I look at other conversations,

I'm having successful conversations to those same servers.

So it's not a firewall thing

where that port is being blocked

and I cannot get access to that server on the other side.

So what I do in this instance is I'm always interested in

where these resets are coming from.

So just for now, I'm actually going

to remove sequence number.

Let's remove that column,

remove ack number, remove that column.

And what I'm gonna do is I'm gonna put the IP TTL back in.

There we go, IP TTL.

So what do we notice about those resets?

64, all the resets have 64

when they're allegedly coming from these remote servers.

That means that these resets are unrouted,

that means something local is actually sending that reset.

So I'm gonna take a look at one of them.

I'm gonna right-click Conversation Filter TCP,

and I'm going to just filter in on this.

So here a SYN goes and a reset ack comes back,

SYN goes, reset ack comes back,

and it happens almost immediately.

So there's no latency here, 267 microseconds.

So I know this is unrouted.

So now if I come down here to the MAC addresses,

see now the MAC addresses come into play.

These resets are originating from this sonic wall.

The sonic wall is the gateway.

So at layer two, you're gonna see the sonic wall

be the destination or source for the good packets too.

But in this case, it's not just the sonic wall

is passing these resets, it's originating them.

So what we saw is that some SYNs were being reset

by the sonic wall, but not all.

So we didn't know exactly why at that moment,

but it led us in the right direction.

And this is a takeaway point from this.

Sometimes packets will help you to zero in on the device

that's causing the problem,

but they won't always give you root cause.

You might have to look a little deeper within that device.

So we went ahead and jumped on that device's management.

And what we found is there's a little setting

called TCP connections per user,

and that value is set to 100.

So every user would work up to TCP connections,

up to 100 connections,

and then 101 would get reset, 102 reset.

You'd have to wait until old TCP connections

were either FINed, reset, or timed out

before you could open up a new SYN somewhere.

So we involved the vendor, we called up SonicWall who said,

hey, here's the parameters, here's the sonic wall,

here's the resources, here's what that client is doing,

here's how much load they're putting on it.

So what do you think?

And they said, oh, you can increase that number.

You can bop it way up.

In fact, the default setting is 500, so you should be okay.

The intent here is just to make it to where an end user

inside that firewall won't be used as a botnet

and just start blasting SYNs out into the internet

or to a lateral device.

It's designed to help protect against that type of behavior.

But in this case, that number was set way too low,

and it was causing people to not be able to connect

to services that they needed.

So this is an example that I look for in packets

when things are just getting reset or dropping unexpectedly.

Resets are something that you want to pay attention to.

Now, just a word of warning,

resets also happen in normal conditions.

Sometimes it depends on the application.

So just 'cause you see a reset doesn't necessarily mean

all is lost, and that's the root cause.

So what you wanna do is try

to capture the problem when it's working well

and when it's broken.

'Cause you might find when it's working well,

things just shut down with resets.

So that's something that you can use to compare against.

All right, so that was the fifth one.

So whenever I have connectivity problems,

those are the types of things that I'll go looking for.