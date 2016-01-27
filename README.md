## Hackathon Judging app

This is a first stab at building a multi-user judging app for large hackathons. It is inspired by my existing [expo app](http://github.com/nealrs/expo), [MLH](http://mlh.io)'s stack ranking approach to judging, and a conversation Richard and I had during [PennApps](http://pennapps.com) XIII. 

**I WOULD LOVE YOUR HELP IN BUILDING THIS. PULL REQUESTS WELCOME.**

There are three major parties when it comes to judging: 

1. Hackers ~ they want judges to see & vote for their projects.
2. Organizers ~ they want to assign judges to review projects & collect their scores in order to identify the top 10-20 projects.
3. Judges ~ they need to know which projects to judge, where to find the hackers who made them, and a way to score them.

My existing expo app already helps organizers connect hackers & judges by making it super easy to assign a unique table number to each project. (It also helps sponsor judges identify which projects use their APIs - more on this later.) The problem is, organizers are currently using spreadsheets & paper slips to assign judges to tables, log votes, and track judge progress. Frankly, it's a mess. And at PennApps / Mhacks scale with 40+ judges, 2 expos, and ~200 projects, it's nearly impossible. Any hackathon without over 50 projects could use a judging app. 

## Key components

### Organizer app

Organizers will use this desktop webapp as a dashboard for setting up judging, monitoring progress, and finally, exporting the data. It should allow a single organizer to: 

1. Import project info from the expo app (name, table, expo, sponsor prizes, category, other custom info) 
2. Sanction judges (name, email, phone, id) 
3. Assign blocks of tables to each judge to judge -- ensuring that each project is seen by at least 2 unique judges
4. Track judge progress in realtime or near-realtime
5. Add late submissions + manually assign judges to tables
6. Export data to CSV for later processing
7. Maintain records of judge assignments & scores in case of disputes
8. Enable the organizer to notify judges of updates to judging / upcoming deadlines / etc.

### Mobile app

Judges will use this mobile optimized webapp to navigate the expo and score projects. It should: 

1. Provide a unique URL to each judge
2. Maintain data integrity / security
3. Support multi-expo hackathons (PennApps / MHacks)
4. Ensure that a judge reviews all assigned tables
5. Identify each project by expo, table, and project name
6. Give judges 3 clear options for each table: vote, pass, no show (last option is for hackers who aren't at their table)
7. Enable judge to change mind before submitting score
8. Enable judge to submit scores
9. Be capable of receiving messages from organizers / organizer app
10. Work on mobile, tablet, desktop 

## Data structure

So far, I've been thinking playing with a combination of CSV/relational & NoSQL datastructures. It's not pretty, but I'd like to keep data in JSON and human input in tablular format if possible. 

Here's how I (so far) envision the data structure (assumes firebase):

```json
{
  "projects": [
    {
      "id": "p1",
      "expo" : "1",
      "table" : "1",
      "name" : "Kontext",
      "sponsorPrizes" :
        [
          "Best Houndify Hack",
          "1st place - Sendgrid",
          "Twilio - best SMS project",
          "Best Use of Comcast Everyblock API"
        ],
      "custom1" : "Hardware",
      "custom2" : ""
    },

    {
      "id" : "p2",
      "expo" : "1",
      "table" : "2",
      "name" : "Protray.me",
      "sponsorPrizes" :
        [
          "Best Houndify Hack",
          "Twilio - best SMS project"
        ],
      "custom1" : "Humor",
      "custom2" : ""
    },

    {
      "id" : "p3",
      "expo" : "1",
      "table" : "3",
      "name" : "EveryChat",
      "sponsorPrizes" :
        [
          "Best Use of Comcast Everyblock API"
        ],
      "custom1" : "Social Impact / Civic Hacking",
      "custom2" : ""
    }
  ],

  "judges": [
    {
      "id" : "a5x7a",
      "name" : "Neal Shyam",
      "phone" : "1115551212",
      "email" : "neal@neal.rs"
    },

    {
      "id" : "b4x41",
      "name" : "Richard Murby",
      "phone" : "1115551212",
      "email" : "rich@murbysworld.com"
    },

    {
      "id" : "y7g57",
      "name" : "Holly Tiwari",
      "phone" : "1115551212",
      "email" : "holly@gmail.com"
    }
  ],

  "scores": [
    {
      "id": "p1",
      "round1" : {
        "j1" : "a5x7a",
        "j2" : "y7g57",
        "s1" : "1",
        "s2" : "2"
      },
      "round2": {
        "j1" : "b4x41",
        "j2" : "a5x7a",
        "s1" : "1",
        "s2" : "2"
      }
    },

    {
      "id": "p2",
      "round1": {
        "j1" : "a5x7a",
        "j2" : "y7g57",
        "s1" : "3",
        "s2" : "3"
      },
      "round2": {
        "j1" : "b4x41",
        "j2" : "a5x7a",
        "s1" : "1",
        "s2" : "2"
      }
    },

    {
      "id": "p3",
      "round1": {
        "j1" : "a5x7a",
        "j2" : "y7g57",
        "s1" : "3",
        "s2" : "3"
      },
      "round2": {
        "j1" : "b4x41",
        "j2" : "a5x7a",
        "s1" : "0",
        "s2" : "0"
      }
    }
  ]
}

```

## Considerations / ideas

- Use firebase for 2way realtime data sync.
- use URL params for judge id, round #, and sponsor prizes/ category
