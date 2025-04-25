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

**Using Python for further analysis**

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


**Flow Safety Checker**
def is_safe_velocity(velocity, min_speed=0.3, max_speed=2.0):  
    return min_speed <= velocity <= max_speed  

print(f"Safe flow? {is_safe_velocity(V)}")  

**Flow Rate Calculator (Discharge)**

def calculate_discharge(velocity, width, depth):
    """Computes discharge (m³/s) for rectangular channels.
    
    Args:
        velocity (float): Flow speed from Manning's equation (m/s)
        width (float): Channel width (m)
        depth (float): Water depth (m)
    
    Returns:
        float: Discharge in cubic meters per second (m³/s)
    """
    cross_sectional_area = width * depth
    return cross_sectional_area * velocity

# Example: 5m wide, 2m deep channel @ 1.23 m/s
Q = calculate_discharge(1.23, 5, 2)
print(f"Water discharge: {Q:.1f} m³/s")  # Output: 12.3 m³/s

# Safety check combined with discharge
if not is_safe_velocity(1.23):
    print("Warning: Unsafe velocity for this discharge!")
else:
    print(f"System stable: {Q:.1f} m³/s flowing safely")

**Critical Depth Calculator**
def critical_depth(discharge, width, gravity=9.81):
    """Calculates critical depth (m) for rectangular channels.
    
    Args:
        discharge (float): Flow rate (m³/s) from calculate_discharge()
        width (float): Channel width (m)
        gravity (float): Earth's gravity (default 9.81 m/s²)
    
    Returns:
        float: Depth where flow becomes unstable (m)
    """
    q = discharge / width  # Unit discharge (m²/s)
    return (q ** 2 / gravity) ** (1/3)

# Using previous discharge value (12.3 m³/s)
d_crit = critical_depth(12.3, 5)
print(f"Critical depth: {d_crit:.2f} m")  # Output: 0.86 m

# Compare with actual depth
actual_depth = 2.0
if actual_depth > d_crit:
    print("Flow is tranquil (subcritical)")
else:
    print("Flow is rapid (supercritical) - Danger!")

**Hydraulic Jump Prediction**
def flow_regime(depth, critical_depth):
    return "Subcritical" if depth > critical_depth else "Supercritical"

**Design Integration**

    def complete_analysis(width, depth, slope, roughness):
    V = rectangular_flow(roughness, width, depth, slope)
    Q = calculate_discharge(V, width, depth)
    d_crit = critical_depth(Q, width)
    return {
        'Actual Depth': depth,
        'Critical Depth': d_crit,
        'Regime': flow_regime(depth, d_crit)
    }

**Hydraulic Calculation Functions**    

import csv
from pathlib import Path
import matplotlib.pyplot as plt

# 1. First include all required hydraulic functions
def rectangular_flow(roughness, width, depth, slope):
    """Calculate flow velocity for rectangular channels using Manning's equation"""
    area = width * depth
    wet_perimeter = width + 2 * depth
    hydraulic_radius = area / wet_perimeter
    return (1 / roughness) * (hydraulic_radius ** (2/3)) * (slope ** 0.5)

def is_safe_velocity(velocity, min_speed=0.3, max_speed=2.0):
    """Check if velocity is within safe range"""
    return min_speed <= velocity <= max_speed

# 2. Now add the file handling functions
def save_channel_data(filename, width, depth, slope, roughness, velocity):
    """Saves channel analysis results to CSV"""
    try:
        filepath = Path(filename)
        file_exists = filepath.exists()
        
        with open(filepath, 'a', newline='') as f:
            writer = csv.writer(f)
            if not file_exists:
                writer.writerow(['Width(m)', 'Depth(m)', 'Slope', 'Roughness', 'Velocity(m/s)'])
            writer.writerow([width, depth, slope, roughness, round(velocity, 3)])
            
        print(f"Data saved to {filepath}")
        
    except Exception as e:
        print(f"Error saving file: {str(e)}")

def load_channel_data(filename):
    """Loads channel data from CSV"""
    try:
        with open(filename, 'r') as f:
            return list(csv.DictReader(f))
    except FileNotFoundError:
        print(f"File {filename} not found - creating new dataset")
        return []

# 3. Example usage
data_file = "channel_analysis.csv"

# Calculate flow
V = rectangular_flow(roughness=0.013, width=5, depth=2, slope=0.001)
print(f"Calculated velocity: {V:.2f} m/s")
print(f"Safety check: {'Safe' if is_safe_velocity(V) else 'Dangerous'}")

# Save results
save_channel_data(data_file, width=5, depth=2, slope=0.001, roughness=0.013, velocity=V)

# Load and display historical data
history = load_channel_data(data_file)
if history:
    print("\nPrevious analyses:")
    for row in history[-3:]:  # Show last 3 entries
        print(f"{row['Width(m)']}m width, {row['Velocity(m/s)']} m/s")
print(flow_regime(2.0, d_crit))  # Output: Subcritical
