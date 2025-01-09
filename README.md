# Rutherford Scattering Simulation

This project simulates Rutherford scattering using Monte Carlo methods. It provides an interactive interface to explore the scattering behavior of alpha particles when they pass through a thin foil. The simulation includes options for adding an analytical curve and a fit curve for comparison. Various parameters such as the number of alpha particles, kinetic energy (KE), foil thickness, and the element of the foil can be adjusted dynamically using sliders.

## Features
- **Monte Carlo Simulation**: Simulates the scattering of alpha particles.
- **Analytical Curve**: Adds a theoretical curve to the simulation for comparison.
- **Fit Curve**: Computes and overlays a fit curve for the simulated data.
- **Interactive Sliders**: Adjust key parameters such as:
  - Number of alpha particles
  - Kinetic energy (KE)
  - Thickness of the foil
  - Element of the foil (e.g., gold, silver, etc.)

## Requirements
To run the project, you need:

- Python 3.10 or later
- Required Python libraries (install using `pip`):
  - `numpy`
  - `matplotlib`
  - `scipy`
  - `ipywidgets` (for interactive sliders)

## Usage
Run the Python script to start the simulation:
```bash
python rutherford_simulation.py
```

### User Interface
1. **Adjust Parameters**:
   - Use sliders to modify:
     - Number of alpha particles
     - Kinetic energy (KE)
     - Foil thickness
     - Element of the foil
2. **Add Analytical Curve**:
   - Click the "Add Analytical Curve" button to display the theoretical curve.
3. **Add Fit Curve**:
   - Click the "Add Fit Curve" button to compute and display a fit curve based on the simulated data.

### Screenshots


## Theory
The simulation is based on the principles of Rutherford scattering, where alpha particles interact with the nucleus of atoms in the foil. The Monte Carlo approach is used to randomly sample scattering angles based on the Rutherford scattering formula:

\[
\frac{d\sigma}{d\Omega} \propto \frac{1}{(KE \cdot \sin^4(\theta/2))}
\]

where:
- \(d\sigma/d\Omega\) is the differential cross-section
- \(KE\) is the kinetic energy of the alpha particle
- \(\theta\) is the scattering angle

## Contributing
Contributions are welcome! To contribute:
1. Fork the repository.
2. Create a new branch for your feature or bug fix.
3. Commit your changes and push to your fork.
4. Submit a pull request.

## License
This project is licensed under the MIT License. See the LICENSE file for details.

## Acknowledgments
- Inspired by Rutherford's pioneering experiments.
- Special thanks to open-source libraries and the Python community.

## Contact
For any questions or suggestions, please contact:
- **Your Name**
- Email: your.email@example.com
- GitHub: [yourusername](https://github.com/yourusername)
