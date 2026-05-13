# Exoplanet Data — README

A cleaned slice of the **NASA Exoplanet Archive** — confirmed exoplanets
discovered by *any* method, between 1995 and 2026. Each row is one
planet.

## What was cleaned

The raw archive download had 92 columns and 39,852 rows. The row count
is misleading: each planet has multiple parameter sets from different
studies, so there are far more rows than planets. We kept only the
"default" parameter set per planet (`default_flag == 1`), giving 6,286
unique planets. From there:

- Kept 10 student-friendly columns: identifiers, discovery method,
  discovery year, and 6 physical quantities.
- Dropped rows missing the **core identifiers**, orbital period, or
  host-star temperature. Without those, there's nothing to plot.
- Dropped rows that lacked **both** planet radius and planet mass —
  again, no measurement to plot.

What's left: **5,487 planets** across **10 columns**.

## Columns

| # | Column            | Units              | Description                                                                       |
| - | ----------------- | ------------------ | --------------------------------------------------------------------------------- |
| 0 | `pl_name`         | text               | Planet name (e.g., `Kepler-22 b`).                                                |
| 1 | `hostname`        | text               | Host star name. Multiple planets in the same system share a host name.            |
| 2 | `discoverymethod` | text               | How the planet was first detected (see "Discovery methods" below).                |
| 3 | `disc_year`       | year               | Year of the discovery. The earliest planets in this file are from 1995.           |
| 4 | `pl_orbper`       | days               | Orbital period — how long one "year" is on the planet.                            |
| 5 | `pl_rade`         | Earth radii        | Planet radius. Earth = 1, Jupiter ≈ 11.2. **May be NaN.**                         |
| 6 | `pl_bmasse`       | Earth masses       | Planet mass. Earth = 1, Jupiter ≈ 318. **May be NaN.**                            |
| 7 | `pl_eqt`          | K                  | Equilibrium temperature — surface temp in radiative balance, no atmosphere. **May be NaN.** |
| 8 | `st_teff`         | K                  | Host star effective temperature. Sun ≈ 5778 K; cool M dwarfs ≈ 3000 K.            |
| 9 | `sy_dist`         | parsecs            | Distance from us to the planet's host star. 1 pc ≈ 3.26 light-years. **May be NaN.** |

## Discovery methods present

| Method                      | Count | What it measures                                                              |
| --------------------------- | ----: | ----------------------------------------------------------------------------- |
| Transit                     | 4533  | Dip in starlight when the planet crosses in front of the star — gives radius. |
| Radial Velocity             |   899 | Wobble of the star caused by the planet's gravity — gives mass.               |
| Transit Timing Variations   |    27 | Tiny shifts in transit times caused by other planets pulling on the transiter. |
| Imaging                     |     7 | Direct photo of the planet (usually with the star blocked out).               |
| Eclipse Timing Variations   |     6 | Shifts in eclipse timings in binary star systems.                             |
| Orbital Brightness Modulation |   6 | Reflected/emitted light variations as the planet orbits.                      |
| Astrometry                  |     4 | Sideways wobble of the star on the sky.                                       |
| Microlensing                |     2 | Gravitational lensing of a background star by the planet's host.              |
| Pulsation Timing Variations |     2 | Shifts in stellar pulsations caused by planets.                               |
| Pulsar Timing               |     1 | The original method — shifts in pulsar timing (1992, around a dead neutron star). |

## Things to watch for

- **NaN values exist on purpose.** Different detection methods measure
  different things. Transit gives you radius but usually not mass.
  Radial velocity gives you mass but never radius. Microlensing rarely
  gives you either with high precision. When a quantity is missing,
  it's because that method physically can't measure it, not because
  the data was sloppy.

  → For any plot that uses `pl_rade`, `pl_bmasse`, or `pl_eqt`, build a
    Boolean mask first that filters out NaN. The notebook walks you
    through this in Exercise 2 (Pine §4.4.4 Boolean indexing — same
    pattern you used in the control flow assignment).

- **Equilibrium temperature is *not* surface temperature.** It assumes
  no atmosphere and an idealized energy balance. Venus has a `pl_eqt`
  of about 230 K but a real surface temperature of about 730 K because
  of its CO₂ atmosphere. Treat `pl_eqt` as a rough sorting tool.

- **`pl_bmasse` is sometimes a *minimum* mass.** For radial-velocity
  detections we measure the planet's mass times the sine of its
  orbital inclination — the *true* mass could be higher. The "b" in
  the column name is the NASA archive convention.

- **Detection bias is real.** The catalog over-represents
  short-period, large-radius planets simply because those are the
  easiest to detect by transit. It under-represents long-period, small
  planets — but those almost certainly exist; we just don't see them
  yet. Exercises 3 and 4 will make this bias visible.

## Source

NASA Exoplanet Archive — https://exoplanetarchive.ipac.caltech.edu/

Downloaded May 13, 2026.
