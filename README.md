building an f1 drivers (and possibly constructors)ratings for the past 5 years starting from the Monaco Race week
test project, don't judge me too harshly, im a novice data analyst 

"""
================================================================================
F1 PERFORMANCE INDEX DASHBOARD
================================================================================
An interactive Streamlit app that models driver + constructor attributes,
computes an "Overall Performance Index" (OPI) adjusted for a chosen track
profile, and visualizes the result against simulated grid rivals.

--------------------------------------------------------------------------------
HOW TO RUN
--------------------------------------------------------------------------------
1. pip install streamlit pandas plotly numpy
2. streamlit run f1_opi_dashboard.py

--------------------------------------------------------------------------------
INTEGRATING REAL TELEMETRY WITH `fastf1` (FUTURE WORK)
--------------------------------------------------------------------------------
This app currently runs on hand-authored "dummy" stats (60-99 scale) so it
works offline with zero dependencies on live data. To replace these with
real numbers derived from telemetry, the FastF1 library
(https://docs.fastf1.dev) is the natural next step. FastF1 wraps the F1
live timing API and Ergast historical data, exposing lap-by-lap telemetry,
sector times, tyre data, and session results.

A rough integration plan:

1. INSTALL & CACHE
   pip install fastf1
   import fastf1
   fastf1.Cache.enable_cache('./f1_cache')  # avoids re-downloading sessions

2. PULL A SESSION
   session = fastf1.get_session(2024, 'Monza', 'Q')  # or 'R' for race
   session.load()

3. DERIVE DRIVER ATTRIBUTES FROM TELEMETRY
   - Pace: normalize each driver's best lap time (session.laps.pick_driver(...))
     relative to the session's fastest lap -> scale into a 60-99 band.
   - Racecraft: derive from race-session overtakes/positions gained
     (compare session.results grid position vs finish position across
     multiple races) and from average position changes per stint.
   - Awareness: approximate using incident/safety-car-neutral lap deltas,
     or penalty/incident counts from session.race_control_messages.
   - Experience: pull career start counts from the Ergast API
     (https://ergast.com/mrd/) keyed by driver code, then bucket into score.

4. DERIVE CONSTRUCTOR ATTRIBUTES FROM TELEMETRY
   - Aero Efficiency: compare top-speed trap data on low-drag circuits
     (session.laps['SpeedST']) vs cornering-speed retention on high-speed
     corners -> a high-speed-to-cornering speed ratio proxy.
   - Mechanical Grip: lateral G estimates from telemetry channel
     (session.laps.get_telemetry()) through slow/medium corners.
   - Vehicle Stability: variance of lap times / sector times across a
     stint (lower variance -> more stable platform).
   - Reliability: count of DNFs / mechanical retirements per season pulled
     from Ergast `status` field, converted into a reliability score
     (fewer DNFs -> higher score).

5. REPLACE THE STATIC DataFrame
   Swap `build_default_dataset()` below for a function that loads a cached
   FastF1/Ergast pull, runs the normalization steps above, and returns a
   DataFrame with the *same column schema* used here (Pace, Racecraft,
   Awareness, Experience, AeroEfficiency, MechanicalGrip,
   VehicleStability, Reliability). Because the rest of the app (the OPI
   math, the radar chart, the gauge, etc.) only depends on that schema,
   no other code needs to change.

For now, everything below uses curated example stats so the app is fully
functional without any network calls or API keys.
================================================================================
"""
