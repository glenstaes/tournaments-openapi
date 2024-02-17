# Functional Description

This document describes the functionality of the system.

## Introduction

The system is a web application that supports the organization of tournaments.
It allows users to create and manage tournaments, and to participate in them.

## Use Cases

The main focus is to support a yearly padel tournament, but the system should be flexible enough to support other
types of tournaments and sports.

## Features

A tournament consists of one or more divisions. A division is a strict separation between players or teams based on
specific criteria which are not managed by the application. For example, one could use the age of the players to create
a division for players under 18 and another for players over 18. Another example could be based on skill level, where
one division is for amateur players and another for professional players. It is up to the organiser or player to enroll
in the correct division.

Within a division, there are one or more stages. A stage indicates the stage of the tournament. For example, a common
use case is to have a group stage followed by a knockout stage. The group stage is used to determine the seeding of the
knockout stage.

The system should focus on supporting the following sequential stages:

- A group stage followed by a knockout stage
- A group stage followed by a second group stage
- Only a group stage
- Only a knockout stage

It should however be flexible enough to support more than two stages.

All stages should lead to a ranking of the players or teams which will either be the final ranking of the tournament
or the seeding for the next stage.

A team has a name. Initially, this single field will contain a custom name for the team, the name of both players in
case of doubles or the name of a single player in case of singles. A team can be assigned a strength (optional), which
can help determine the seeding of the players or teams during the first stage of the division.

## Flow

The process starts with the creation of a new tournament. The organiser will then continue with the creation of the
division and the enrollment of players in a division. Before a division can be started, the organiser must define
the different stages of the division and determine the seeding of the first stage. The seeding can be done manually
or automatically based on the given strength of the players or teams. When done automatically, the organiser can still
verify the seeding and make changes if necessary.

After seeding the players or teams, the organiser can verify and start the first stage. From now on, it is no longer
possible to remove the enrollment of a player or team from the division. The organiser can however forfeit a team or
individual matches of a team.

Once the tournament has started, the organiser can enter the results of matches. For group stages, the system will
automatically update the current ranking when the a match result has been entered. For knockout stages, the system
will automatically determine the winner of the match and update the next round. In no case will a match result cause a
change in the next stage of a division.

> TODO: investigate whether it is possible to have a match result that causes a change in the next stage of a division.
> Sometimes, it is possible that the knockout stage can already be started with matches that are known before the
> previous stage has finished. This is often done to speed up the tournament or catch up for lost time because of
> delays. Please refer to the examples at the bottom of this document how this is possible in real life.

After a stage has finished, the organiser can verify the final ranking of the stage and start the next stage if there
is one.

## Automatic seeding determination

When players or teams are assigned a strength, the system can automatically determine the seeding of the first stage.
Players with the highest strength will be seeded first. If two players or teams have the same strength, the system will
randomly determine the seeding for those players or teams.

## Examples

### Example 1: Group stage followed by a knockout stage

In this scenario, the division starts with a group stage and the best two players or teams of each group will continue
to the knockout stage.

#### Group stage

In the group stage, players are divided into groups based on their strength. Here, the Chinese system is used to divide
players over multiple groups. This means that the top seeded player will be placed in group A as the first player, the
second seeded player will be placed in group B as the first player, the third seeded player will be placed in group C as
the first player and so on. When all groups have been filled with a first player, the next seeded player will be placed
in the last group as second player, the next seeded player will be placed in the second to last group as second player,
and so on until group A has two players. The next seeded player will be placed in group A again, then B, then C, and so
on until all players have been placed in a group. This ensures that an equal distribution of strength is achieved over
all groups.

Players seed:

1. Player 1 (strength 10)
2. Player 2 (strength 9)
3. Player 3 (strength 8)
4. Player 4 (strength 7)
5. Player 5 (strength 6)
6. Player 6 (strength 5)
7. Player 7 (strength 4)
8. Player 8 (strength 3)
9. Player 9 (strength 2)
10. Player 10 (strength 1)

Group composition:

| Group A | Group B | Group C |
|---------|---------|---------|
| Seed 1  | Seed 2  | Seed 3  |
| Seed 6  | Seed 5  | Seed 4  |
| Seed 7  | Seed 8  | Seed 9  |
|         |         | Seed 10 | 

In theory, this also means that the winner of group A is stronger than the winner of group B, which in turn is stronger
than the winner of group C. This makes it possible to determine a seeding ranking for the knockout stage. When two
players advance to the knockout stage, the following seeding can be determined:

1. Winner of group A
2. Winner of group B
3. Winner of group C
4. Runner-up of group C
5. Runner-up of group B
6. Runner-up of group A

#### Knockout stage

In this example, 6 players advance to the knockout stage. In the knockout stage, a bracket system is applied instead of
groups. The seeding has been determined by the group stage before.

Within a knockout stage, each initial position in the bracket matches with a seeding position. Since 6 players advance
to the knockout stage, the bracket will have 8 positions available. To determine a spread of strength on the bracket,
ensuring that the best two players don't already meet in the quarter final but only in the final, the following seeding
positions are applied.

```
----------
Seed 1    |
          |
          |----------
          |          |
Seed 8    |          |
----------           |
                     |----------
----------           |          |
Seed 5    |          |          |
          |          |          |
          |----------           |
          |                     |
Seed 4    |                     |
----------                      |
                                |
                                |----------
----------                      |
Seed 3    |                     |
          |                     |
          |----------           |
          |          |          |
Seed 6    |          |          |
----------           |          |
                     |----------
----------           |
Seed 7    |          |
          |          |
          |----------
          |
Seed 2    |
----------                
```

The next step is to fill out the seeds in the bracket with the seeded players. (W = winner, R = runner-up)
You will notice that players will never be able to meet each other again in the first knockout round if they
played each other in the group stage.

```
----------
W Group A |
          |
          |----------
          |          |
          |          |
----------           |
                     |----------
----------           |          |
R Group B |          |          |
          |          |          |
          |----------           |
          |                     |
R Group C |                     |
----------                      |
                                |
                                |----------
----------                      |
W Group C |                     |
          |                     |
          |----------           |
          |          |          |
R Group A |          |          |
----------           |          |
                     |----------
----------           |
          |          |
          |          |
          |----------
          |
W Group B |
----------                
```

Since there are two empty slots in the bracket (seed 7 and 8, for which we didn't have a player seeded), this means
that the winner of Group A and the winner of Group B will have a bye in the first round. In result, they will advance
automatically to the next round.

#### Ranking determination

From a knockout stage, it is perfectly possible to determine a full ranking with all players from the knockout stage.
It perfectly makes sense that the winner of the final is ranked first and the loser of the final is ranked second.
Further down in the ranking, you would think that it is harder to determine the ranking of the players. However, there's
a point of view that can be applied perfectly.

The player that lost in the semi final against the loser of the final can be considered weaker than the player that lost
to the winner of the tournament because the latter lost against a stronger player. This means that the player that lost
to the winner of the tournament will come in 3rd and the other semi finalist will come in 4th. This system can be
applied to all players in the knockout stage (see example 2 for a bigger knockout example).

The final ranking of the tournament will thus be:

1. Winner of the final
2. Loser of the final
3. Loser of the semi final against the winner of the final (player in ranking 1)
4. Loser of the semi final against the loser of the final (player in ranking 2)
5. Loser of the quarter final against the highest ranked player in the top 4
6. Loser of the quarter final against the second highest ranked player in the top 4

### Example 2: One big knockout stage

In this scenario, the division starts with a knockout stage. The seeding of the knockout stage is determined by the
strength of the players.

#### Knockout stage with 29 players

Players seed:

| Pos | Player                  | | Pos | Player                  | | Pos | Player                 |
|-----|-------------------------|-|-----|-------------------------|-|-----|------------------------|
| 1   | Player 1 (strength 29)  | | 11  | Player 11 (strength 19) | | 21  | Player 21 (strength 9) |
| 2   | Player 2 (strength 28)  | | 12  | Player 12 (strength 18) | | 22  | Player 22 (strength 8) |
| 3   | Player 3 (strength 27)  | | 13  | Player 13 (strength 17) | | 23  | Player 23 (strength 7) |
| 4   | Player 4 (strength 26)  | | 14  | Player 14 (strength 16) | | 24  | Player 24 (strength 6) |
| 5   | Player 5 (strength 25)  | | 15  | Player 15 (strength 15) | | 25  | Player 25 (strength 5) |
| 6   | Player 6 (strength 24)  | | 16  | Player 16 (strength 14) | | 26  | Player 26 (strength 4) |
| 7   | Player 7 (strength 23)  | | 17  | Player 17 (strength 13) | | 27  | Player 27 (strength 3) |
| 8   | Player 8 (strength 22)  | | 18  | Player 18 (strength 12) | | 28  | Player 28 (strength 2) |
| 9   | Player 9 (strength 21)  | | 19  | Player 19 (strength 11) | | 29  | Player 29 (strength 1) |
| 10  | Player 10 (strength 20) | | 20  | Player 20 (strength 10) | |     |                        |

The smallest bracket available for 29 players is a bracket with 32 positions. This means that the first 3 seeded players
will have a bye in the first round.

[Check this wikipedia template for a bracket with 32 positions](https://en.wikipedia.org/wiki/Template:32TeamBracket)

#### Ranking determination

1. Winner of the final
2. Loser of the final
3. Loser of the semi final against the winner of the final (player in ranking 1)
4. Loser of the semi final against the loser of the final (player in ranking 2)
5. Loser of the quarter final against the highest ranked player in the top 4
6. Loser of the quarter final against the second highest ranked player in the top 4
7. Loser of the quarter final against the third highest ranked player in the top 4
8. Loser of the quarter final against the fourth highest ranked player in the top 4
9. Loser of the 8th final against the highest ranked player in the top 8
10. Loser of the 8th final against the second highest ranked player in the top 8
11. Loser of the 8th final against the third highest ranked player in the top 8
12. Loser of the 8th final against the fourth highest ranked player in the top 8
13. Loser of the 8th final against the fifth highest ranked player in the top 8
14. Loser of the 8th final against the sixth highest ranked player in the top 8
15. Loser of the 8th final against the seventh highest ranked player in the top 8
16. Loser of the 8th final against the eighth highest ranked player in the top 8
17. Loser of the 16th final against the highest ranked player in the top 16
18. Loser of the 16th final against the second highest ranked player in the top 16
19. Loser of the 16th final against the third highest ranked player in the top 16
20. Loser of the 16th final against the fourth highest ranked player in the top 16
21. Loser of the 16th final against the fifth highest ranked player in the top 16
22. Loser of the 16th final against the sixth highest ranked player in the top 16
23. Loser of the 16th final against the seventh highest ranked player in the top 16
24. Loser of the 16th final against the eighth highest ranked player in the top 16
25. Loser of the 16th final against the ninth highest ranked player in the top 16
26. Loser of the 16th final against the tenth highest ranked player in the top 16
27. Loser of the 16th final against the eleventh highest ranked player in the top 16
28. Loser of the 16th final against the twelfth highest ranked player in the top 16
29. Loser of the 16th final against the thirteenth highest ranked player in the top 16