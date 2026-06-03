import streamlit as st

--- Page Setup ---
st.set_page_config(page_title="EV Charger Cost Optimizer", page_icon="⚡", layout="centered")
st.title("⚡ EV Charger Optimizer")
st.subheader("Subang Jaya & Local Station Recommendations")
st.write("Find the best charger that matches your battery level, timeline, and wallet budget.")

--- Mock Station Database ---
STATIONS = [
{
"name": "Subang Parade (ParkEasy Hub)",
"type": "AC",
"power_kw": 22,
"rate_per_kwh": 1.00,
"rate_per_min": 0.00,
"link": "https://www.plugshare.com/location/587223"
},
{
"name": "Sunway Pyramid (ChargeSini Hub)",
"type": "AC",
"power_kw": 22,
"rate_per_kwh": 0.00,
"rate_per_min": 0.40,
"link": "https://www.chargesini.com/"
},
{
"name": "The Summit USJ",
"type": "DC",
"power_kw": 60,
"rate_per_kwh": 1.50,
"rate_per_min": 0.00,
"link": "https://cardog.app/tools/charging/my/subang-jaya-10"
},
{
"name": "UOA Business Park Glenmarie (DC Handal)",
"type": "DC",
"power_kw": 200,
"rate_per_kwh": 1.70,
"rate_per_min": 0.00,
"link": "https://cardog.app/tools/charging/my/subang-jaya-10"
}
]

--- Sidebar Inputs: Vehicle Specs ---
st.sidebar.header("🚗 1. Your Vehicle Details")
battery_capacity = st.sidebar.number_input("Battery Size (kWh)", min_value=15.0, max_value=120.0, value=60.5, step=0.5)
current_soc = st.sidebar.slider("Current Battery (%)", min_value=0, max_value=99, value=20)
max_ac_limit = st.sidebar.selectbox("Max Car AC Charger Speed (kW)", [7.0, 11.0, 22.0], index=0)

--- Main Screen Inputs: Constraints ---
st.write("---")
st.markdown("### ⏱️ Enter Your Current Constraints")
col1, col2 = st.columns(2)

with col1:
user_time_limit = st.number_input("Max Waiting Time (Minutes)", min_value=10, max_value=480, value=45, step=5)
with col2:
user_budget = st.number_input("Max Budget (RM)", min_value=5.0, max_value=200.0, value=30.0, step=5.0)

--- Calculation Logic ---
needed_energy_kwh = battery_capacity * (1 - (current_soc / 100))

results = []
for station in STATIONS:
# Cap the AC charging speed based on user car's bottleneck
actual_speed = min(station["power_kw"], max_ac_limit) if station["type"] == "AC" else station["power_kw"]

# Check max energy delivery possible given user's timeframe
max_energy_by_time = actual_speed * (user_time_limit / 60)
energy_to_charge = min(needed_energy_kwh, max_energy_by_time)

# Calculate delivery timeline
time_needed_mins = (energy_to_charge / actual_speed) * 60

# Cost math
cost = (energy_to_charge * station["rate_per_kwh"]) + (time_needed_mins * station["rate_per_min"])

# Filter configurations based on user thresholds
if cost <= user_budget and time_needed_mins <= user_time_limit:
results.append({
"name": station["name"],
"type": station["type"],
"power": f"{station['power_kw']} kW",
"energy": energy_to_charge,
"time": time_needed_mins,
"cost": cost,
"link": station["link"]
})

Sort by cost ascending
results = sorted(results, key=lambda x: x["cost"])

--- Display Results ---
st.write("---")
st.markdown("### 🏆 Top Affordable Matches For You")

if not results:
st.error("❌ No nearby chargers match your exact budget or time limit. Try increasing your time window or budget layout!")
else:
for idx, charger in enumerate(results, 1):
with st.expander(f"Option #{idx}: {charger['name']} ({charger['type']} {charger['power']}) — RM {charger['cost']:.2f}"):
col1, col2, col3 = st.columns(3)
col1.metric("Est. Bill", f"RM {charger['cost']:.2f}")
col2.metric("Energy Added", f"{charger['energy']:.2f} kWh")
col3.metric("Time Required", f"{charger['time']:.1f} mins")

st.markdown(f"📍 Open Station Mapping Info / Map Link")

st.info("💡 Note: For AC chargers, charging speed is restricted by your car's Onboard Charger Capacity.")