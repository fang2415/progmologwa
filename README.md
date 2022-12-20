# Why `progmologwa`?

## TL;DR: what does `progmologwa` do?

`progmologwa` lets you log exercises and other activities and automatically remembers and calculates stuff from the last time they were performed.  It has been tested with the [Reddit Fitness Basic Beginner Routine](https://thefitness.wiki/routines/r-fitness-basic-beginner-routine/), [Stronglifts 5x5](https://stronglifts.com/), [Cody Lefever's GZCLP](https://thefitness.wiki/routines/gzclp/), and several of [Jim Wendler's 5/3/1 programs](https://thefitness.wiki/5-3-1-primer/).  It also works nicely as a bodyweight and calorie tracker or to keep notes on sessions of activities like cycling, windsurfing, or sleep.  Setting it up requires some patience and for you to get your hands a little dirty with spreadsheet cells, but after that it's designed to keep running smoothly more or less forever.

## Goals

When I started doing some strength training, I wanted to keep track of my exercises using something that let me do the following:

- Read and input data on a(n Android) mobile using only Free Software
- Read and input data on a desktop using only Free Software
- Operate on local devices without requiring cloud storage
- Store information on how to perform any movement
- Store information on how to progress or regress any movement, including linear weight progressions and custom(/technique) progressions
- Schedule any exercise on any day in any order
- Automatically move to the correct progression stage when an exercise is scheduled
- Automatically calculate weight to load on each loading point of an exercise's apparatus
- Specify an allowable range to increase weight for each warmup set and automatically calculate each warmup weight jump using that range
- Each time an exercise is performed, store notes for general prebrief, for general debrief, for the next time the movement is performed, and for the next time the same movement is performed with the same progression scheme
- See all information needed to perform an exercise when it's scheduled
  - Easily paste this info to a text editor for easy reading on a mobile device
- Record an input (such as weight) and an output (such as successful reps) for each exercise
- Track everything in the same format until my device runs out of memory

Most exercise trackers I've seen are either apps that would violate one of the device requirements or are wide-format spreadsheets for specific exercise programs that violate the requirements of allowing arbitrary movements and progressions as well as the requirement of tracking for an indefinite period.  These requirements are hard enough and I'm picky enough about data management that I figured it'd be faster to write my own than to do an exhaustive search for another solution.  (I still don't know if I was right!)

# Usage

Using the spreadsheet requires some data-savviness.

One general note to bear in mind: never *cut* and paste any cells, or delete any cells, in the workbook.  *Copy*-pasting is fine, but cutting will break references.  If you want to move data, you'll have to either copy it to where you want it and delete the contents of the original cells, or copy blank values over the originals.

## Setting up `progmologwa` the first time

If you've just started with a new `progmologwa` spreadsheet, you should add a list of dates to the `date` column in the `dates` sheet.  You can include any dates you want to schedule things for, but including every day starting with the current date and ending a couple years later is a sensible choice.  To make a list of dates, enter the first date in a cell and then drag the corner of the cell downward, checking that the date sequence fills in correctly.  When the `dates` column has been populated, select the second row (the first under the header row) of all the other columns and extend their formulas to the bottom of the date list (you might find the ctrl/cmd-D shortcut useful for this).

Then seed the log by entering the first date from the `dates` sheet in the colorless cell at the top of the `date` column at the left of the `log` sheet.

Next, select all columns of the `log` sheet in the third row (the one under the row that contains the date cell you just populated).  Extend the formulas from this row down for several hundred rows.  (You'll use one row per exercise, so you'll have to repeat this step when you run out of rows; but on the other hand, extra rows make the file size bigger, so don't overdo it.)

You're now ready to add some activities to track.

## Adding new activities for `progmologwa` to track

A new activity can be added by following the tabs from right to left and inserting new rows to each one.  Hopefully most columns are self-explanatory, and many are optional, so with luck the process will be fairly natural without much documentation; but just in case, the details of each action are listed below.

1. Add a new movement at the bottom of any existing entries in the `movement` tab
    1. Add a new unique `movement_id` (most easily, by incrementing the largest existing ID by one)
    2. Complete the other movement columns:

column | meaning
-- | --
movement | A description that makes clear how to perform the movement
max_wup_jump | Maximum jump (of whatever appropriate in-unit) for warmups
min_wup | Minimum units for warmups
min_increment | Minimum increment that can be added
min_wup_increment | Minimum increment to use for warmup jumps
bar_adds | Number of units the empty apparatus adds
loading_points | Number of points on the apparatus that can be loaded

2. Add some new progression stages at the bottom of any existing entries in the `progression_stages` tab
    1. Add a new `stage_id` by incrementing the largest existing ID by one
    2. Extend the `id_check` field to the new row(s) to confirm that the IDs are unique
    3. Complete the rest of the progression stage fields as applicable:

column | meaning
-- | --
progression_descr | A description of this series of stages
success_to_stage | Go to this stage after success (or leave blank to stay put)
fail_to_stage | Go to this stage after non-success (or leave blank to stay put)
instruction | Instructions that make clear what to do in this stage
rest | Description of rest period
skip_wup | TRUE if warmups should be skipped for this stage
roll_notes_forward | TRUE if notes should be hidden and passed on to the next stage
more_is_better | TRUE for things where more units is better, FALSE when less is better
calc_e1rm | TRUE if an Epley estimated 1RM should be calculated for this stage
max_attempt | TRUE if this stage is a max attempt
result_out_start | `n` of the log's first `out_n` to calculate success (inclusive)
result_out_end | `n` of the log's last `out_n` to calculate success (inclusive)
success | Number of out-units that constitutes a success
smash | Number of out-units that constitutes a big success
small_lp_x_incrm | Linearly progress by this many movement increments after success
big_lp_x_incrm | Linearly progress by this many movement increments after big success
add_x_incrm_at_start | Linearly progress by this many movement increments before exercise
propn_at_start | Multiply in-unit by this number before exercise
deload_after_n_fail | Deload after this many failures
deload_by_factor | Deload by multiplying by this much
propn_of_target | Proportion of the slot's target to use for this stage

3. Add a new tracker at the bottom of any existing entries in the `tracker` tab
    1. Add a new `tracker_id` by incrementing the largest existing ID by one
    2. Add a new `tracker_name` that will remind you what the tracker is
    3. Add the `movement_id` of whatever movement you want to track
    4. Under `first_stage_id`, add the `stage_id` of the stage that begins the tracker's progression
    5. Extend the `movement_ref` and `progression_ref` formulas to the new row(s)

4. Add new trackers to the slots for each day
    1. In the `dates` sheet, scroll to the columns beginning with `slot_x_tracker_y`
    2. For each activity you want to track over a period of time, enter its `tracker_id` in one of the `slot_x_tracker_id` columns for those dates
        1. You might find the ctrl/cmd-D shortcut helpful to copy the tracker ID to a large block of time (say, for a month)
        2. If you don't know when you want to stop tracking an activity, you might as well fill it into its slot for every row in the sheet (you can always change it later)

That's everything needed to add a new activity.

## Scheduling tracked activities

In brief: enter the tracker slot numbers you want to schedule into the work slots on the `dates` sheet.  Check the `slot_reference` tab for reference on which trackers are in which tracker slots.

## Logging

The gray columns on the left of the `log` sheet show information of scheduled work for the day: which movement to do, what the stage instructions are, and what the scheduled input is.  You can set the `actual_in` as desired (presumably using the scheduled input as a guide).  The `summary` column has all of this information in a format suitable for pasting into a text file.

Once the work is complete, debrief notes, next steps for the movement and progression, and outputs can be logged.  Notes for next steps will show up the next time the tracker is scheduled, and the outputs will affect the tracker's next scheduled input (depending on the rules set up in the progression sheet).

Less-frequently-used columns are to the right of the output columns: a column for prebrief notes (which will appear in the current row's summary), a column to set a new target (which can be used along with the `propn_of_target` column of the `progression_stages` sheet to calculate scheduled inputs until the target changes), a column to force a certain number of fails, a column which causes a row's notes to be rolled forward to the next time the tracker is scheduled, and a `repeat_next_time` column that causes the row's scheduled values to repeat the next time the tracker appears when the column is set to TRUE (or 1).  The `program_descr` column is simply a text description to be used in external analysis tools.

## Managing file size and updates

The `.fods` file format makes it (sorta) easier for git to track changes to the file, but it will quickly balloon once you put data into it.  You should save the workbook as a standard file format like `.ods` or `.xlsx` once you start using it.

Note that the formulas are a good deal bigger than the values they compute.  If the workbook is getting too large, you can save space and memory by pasting the values of rows that no longer need updating over the formulas.

Unfortunately, I know of no good mechanism to seamlessly update spreadsheets.  When a new version comes out, you'll need to start with a fresh template and paste in your data.  Hopefully this will require only a few copy-pastes per sheet, plus using ctrl/cmd-D to extend the formulas to the bottom of your data.
