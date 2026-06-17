building an f1 drivers (and possibly constructors)ratings for the past 5 years starting from the Monaco Race week
test project, don't judge me too harshly, im a novice data analyst 

# ==============================================================================
# FASTF1 INTEGRATION NOTE (How to transition to real data)
# ==============================================================================
# To replace this mock/tweakable database with live F1 telemetry data:
# 1. Install the library: pip install fastf1
# 2. Initialize caching: fastf1.Cache.enable_cache('path/to/cache')
# 3. Fetch a session: session = fastf1.get_session(2026, 'Monaco', 'Q')
# 4. Load telemetry: session.load(telemetry=True, weather=False)
# 
# Extracting Attributes Programmatically:
# - PACE: Calculate the driver's median lap time delta against the absolute fastest lap.
# - AERO EFFICIENCY: Analyze car speed traces on maximum length straights (e.g., DRS zones).
# - MECHANICAL GRIP: Isolate minimum speeds in low-velocity corner clusters.
# ==============================================================================
