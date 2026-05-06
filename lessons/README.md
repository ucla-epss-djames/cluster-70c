# Planetary Data — Solar System Bodies

This dataset contains physical and orbital properties for the eight planets, plus Earth's Moon and Pluto. The values are transcribed from NASA's *Planetary Fact Sheet* (NSSDC, Goddard Space Flight Center).

## File

- `planetary_data.csv` — comma-separated text, one row per body, header in the first row.

## How to read it in (Pine §5.3.2)

The file is a CSV — exactly the format Pine introduces in §5.3.2 ("Reading Data from an Excel File: CSV Files"). You read it with `np.loadtxt`, the same function used in §5.3.1 for `mydata.txt`. The only differences are `delimiter=','` for the commas and `usecols=` to skip the first column, which contains the body names (text) instead of numbers.

**Load every numeric column at once:**

```python
import numpy as np

# usecols=range(1, 21) means: columns 1 through 20, skipping column 0 (the body name)
(mass, diameter, density, gravity, escape_v,
 rot_period, day_length, dist_sun, perihel, aphel,
 orb_period, orb_v, orb_incl, ecc, obliq,
 temp, pressure, num_moons, rings, magfield) = np.loadtxt(
    "planetary_data.csv",
    delimiter=",", skiprows=1, usecols=range(1, 21), unpack=True)
```

**Or grab just the columns you need** (Pine p. 92, `usecols` keyword):

```python
# Just diameter (col 2) and mass (col 1)
diameter, mass = np.loadtxt("planetary_data.csv",
                            delimiter=",", skiprows=1,
                            usecols=(2, 1), unpack=True)
```

**The body names go in your script as a list** (since `np.loadtxt` reads numbers, not strings — you'll see how to mix them later in Ch. 11 with Pandas):

```python
bodies = ["Mercury", "Venus", "Earth", "Moon", "Mars",
          "Jupiter", "Saturn", "Uranus", "Neptune", "Pluto"]
```

The order of `bodies` matches the order of rows in the CSV.

## Columns (0 = body name, then numeric columns 1–20)

| col | name | units | description |
|---|---|---|---|
| 0 | body | — | Name of the planet/moon |
| 1 | mass | 10²⁴ kg | Total mass |
| 2 | diameter | km | Equatorial diameter |
| 3 | density | kg/m³ | Bulk (mean) density |
| 4 | gravity | m/s² | Surface gravity at the equator |
| 5 | escape_velocity | km/s | Speed needed to escape the body's gravity |
| 6 | rotation_period | hours | Sidereal rotation. **Negative = retrograde** (Venus, Uranus, Pluto) |
| 7 | length_of_day | hours | Average solar day length |
| 8 | distance_from_sun | 10⁶ km | Average orbital distance from the Sun. *For the Moon, this is distance from Earth.* |
| 9 | perihelion | 10⁶ km | Closest approach to Sun (or Earth, for the Moon) |
| 10 | aphelion | 10⁶ km | Farthest distance from Sun (or Earth, for the Moon) |
| 11 | orbital_period | Earth days | Time to complete one orbit |
| 12 | orbital_velocity | km/s | Average orbital speed |
| 13 | orbital_inclination | degrees | Tilt of orbit relative to ecliptic |
| 14 | orbital_eccentricity | 0–1 | How non-circular the orbit is. 0 = circle |
| 15 | obliquity | degrees | Axial tilt |
| 16 | mean_temperature | °C | Average surface temp (cloud-top for gas giants) |
| 17 | surface_pressure | bars | Surface atmospheric pressure. **`NaN` for gas giants** (no solid surface) |
| 18 | num_moons | count | Number of confirmed moons |
| 19 | has_rings | 0 or 1 | 1 = ring system, 0 = none |
| 20 | has_magnetic_field | 0 or 1 | 1 = global field, 0 = none. **`NaN` for Pluto** (unknown) |

## Things to watch for

- **The Moon's distance columns** (`dist_sun`, `perihel`, `aphel`) are measured **from Earth**, not from the Sun. If you're plotting "distance from the Sun" for everything, exclude the Moon (it's at row index 3).
- **Negative rotation periods** indicate retrograde rotation. For "how fast does it spin," take `np.abs()` of the column.
- **`NaN` values** appear where data is missing/undefined. `np.loadtxt` reads them in fine, but operations like `.mean()` will return `NaN` unless you use `np.nanmean()`.

## Source

NASA Goddard Space Flight Center — Planetary Fact Sheet:
https://nssdc.gsfc.nasa.gov/planetary/factsheet/
