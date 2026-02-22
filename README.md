# automatic-bassoon
Testing out an agent that helps you figure out where to eat a meal in NYC with a hybrid LLM + deterministic workflow agent 

Problem: Choosing a restaurant for a group requires:
1. Intent parsing (location, date, party size, constraints)
2. Geocoding a neighborhood
3. Restaurant discovery
4. Popularity and review of that restaurant
5. Reservation feasibility estimation
6. Ranking and tradeoff explanation
7. Actionable booking next steps 

Today this workflow is Multi-app (Google, Maps, Resy, OpenTable), sequential, tool-fragmented, unranked by booking likelihood, and lacking a single-decision quick answer

My solution: build an agentic reasoning layer between messy natural language input and deterministic execution tools, it doesn't work on your behalf but helps give you a single answer

System architecture:
1. Flexible reasoning (LLM) which extracts neighborhood, party size, date/time, validates completeness, handles messy input and explains tradeoffs in output
2. Deterministic execution: geocode (lat, lng), fetch nearby restaurants, aggregate ratings and review counts, estimate reservation likelihood, rank options, and return an structured output 

Step 1: 
Input: "I want to eat in Bowery with 6 people on March 4th at 6:00PM"
Output: {
  "neighborhood": "Bowery",
  "party_size": 6,
  "date": "2025-03-04",
  "time": "18:00",
  "constraints": null
}
Step 2: Geocode Tool 
- Convert neighborhood to lat, lng
- pass structured coordinates downstream
Step 3: restaurant discovery tool
- query google places API
- return candidate restaurants in radius
Step 4: popularity aggregation
- for each restaurant, grab the google rating, review count, price tier, cuisine, and platform presence for booking
Step 5: reservation likelihood heuristic
- logic example - lower availability with large party, high popularity, and prime time
Step 6: ranking
- sorted by reservation likelihood
- popularity
- weighted composte score
Step 7: Structured output
- 3–5 ranked options
- Popularity metrics
- Price tier
- Vibe summary
- Reservation guidance
- Booking routes
- Optional phone script draft

The agentic layer was needed because the problem statement is open-ended and messy. the LLM is there to interpret and resolve amnbiguity, validate required fields, and downstream logic remains deterministic to maintain reproducibility 

limitations - cant book directly on resy or open table, likelihood is heuristic based, limited to google places coverage and only NYC focused 

future improvements - real time booking capabilities, multi-city, logging and evaluation framework 

