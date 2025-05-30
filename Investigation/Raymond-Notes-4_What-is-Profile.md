# What is a Profile in Wireshark?

{Placeholder}

## After create a Profile, what are the most common things to do?

1. Enable the delta time view when working with a new profile.
    
    e.g.: \
    - View > Time Display Format > Seconds Since Previous Displayed Packet
    - View > Time Display Format > Time of Day
    
    Add a dedicated delta-time column to measure intervals between packets and track a running total (or time of day).

    Edit > Preferences > Appearance > Columns > Add > Delta Time displayed

2. Adjust the Pane layout.

    Edit > Preferences > Appearance > Layout

3. Reset the "Default" profile to factory Default.

    Right-click on the profile in the right bottom corner > Edit > Delete the Default profile > Exit Wireshark > Restart Wireshark

## Coloring Traffic

1. Select one of the packets that has the SYN flag set.
2. Go to the Packet Details pane and expand the Transmission Control Protocol section.
3. Expand the Flags section, drag the line of "SYN: set" and drop it into display filter bar.
4. The filter will be added to the display filter bar.
5. Try to remove the default SYN filter and add a new coloring rule for filter "tcp.flags.syn == 1". Modify the background color to a light green and the foreground color to black.
6. Do the same for filter "tcp.flags.fin == 1". Modify the background color to a light blue and the foreground color to black.

## Adding filter buttons

Create a filter in the display filter bar, then click on the "+" button next to the display filter bar.

Now, as we go forward and go through this class, I'd really like you to start adding your own filters up here on the top right.

- Which ones jump out to you in your environment?
- What kind of applications do you commonly filter for?
- What are some IP ranges that you're used to working with?

Those are all great filters to add up above.
Now, if you ever wonder what you did up there,this is where you can just come up and right click and this is where we can go to filter button preferences and we see this window pop up.