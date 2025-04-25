Open-Channel Flow Analysis
Water flowing in rivers, canals, or drains must move at the right speed—not too fast, not too slow. This project helps engineers calculate the safe flow speed to:

🚫 Prevent erosion (water moving too fast digs into the channel).

🚫 Avoid clogging (water moving too slow lets dirt pile up).

How It Works
1. The Problem
If water flows too slow (< 0.3 m/s), mud and sand settle (clogging).

If water flows too fast (> 2.0 m/s), it damages the channel (erosion).

We need to find the safe speed in between.

2. Manning’s Equation 
A simple formula to calculate water speed:

Velocity = (1 / Roughness) × (Hydraulic Radius)^(2/3) × (Slope)^(1/2)

Roughness (n): How smooth or rough the channel is (e.g., concrete = 0.013, dirt = 0.03).

Hydraulic Radius (R): Flow area divided by the wet perimeter (bigger = faster flow).

Slope (S): How steep the channel is (steeper = faster water).

Using Python for further analysis 

Core Velocity Calculation Using Manning’s Equation
def calculate_flow_speed(roughness, hydraulic_radius, slope):  
    # Manning's equation  
    velocity = (1 / roughness) * (hydraulic_radius ** (2/3)) * (slope ** 0.5)  
    return velocity  

# Example: Concrete channel (n=0.013), R=1.1, slope=0.001  
n = 0.013  
R = 1.1  
S = 0.001  

V = calculate_flow_speed(n, R, S)  
print(f"Flow speed: {V:.2f} m/s")  
