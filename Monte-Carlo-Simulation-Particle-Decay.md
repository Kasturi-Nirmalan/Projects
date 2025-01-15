# Monte Carlo Simulation of Particle Decays from the Higgs Boson

## Overview
This project simulates the decay of a particle (such as the Higgs boson) using Monte Carlo methods and analyses the momentum distribution of the decay products, with a Gaussian fit applied to the results. The code creates a particle and simulates the decay process by generating random decay events and fits it onto the momemtum distribution with a Gaussian fit.

The following sections describe how the code works in chunks and the steps involved in simulating particle decay.

## Setting Up the Simulation Environment

```cpp
#include <iostream>
#include <TRandom3.h>
#include <TMath.h>
#include <TLorentzVector.h>
#include <TCanvas.h>
#include <TH1F.h>
#include <TFile.h>
#include <TF1.h>
```
- ```TRandom3```: Used for generating random numbers, which are fundamental to the Monte Carlo simulation.
- ```TMath```: Contains mathematical functions like trigonometry.
- ```TLorentzVector```: Represents 4-vectors used for relativistic kinematics (energy and momentum of particles).
- ```TCanvas```, ```TH1F```, ```TFile```: ROOT classes used for drawing histograms, creating 1D histograms, and saving the results to a file.
- ```TF1```: Used for defining a mathematical function (in this case, a Gaussian) to fit the data.

## Defining constants

We then have to define the mass of the Higgs Boson, which is 125 GeV, as well as the number of events we are going to simulate.

```
double mass_parent = 125.0; // Mass of the decaying particle (Higgs Boson) in GeV
int n_events = 10000; // Number of events to simulate
```

## Random number generator

Using a ROOT class, we make a random number generator, giving it a seed value so that this simulation can be reproduced.

```
TRandom3 rand(123); // Seed value for reproducibility
```

## Creating the histogram

We make a 1D histogram with the axes labelled. 

- ```TH1F```: Creates a 1D histogram for storing the momentum distribution of decay products.
    - ```100, 0, 100```: The histogram will have 100 bins, spanning momentum values from 0 to 100 GeV.

```
TH1F* h_momentum = new TH1F("h_momentum", "Momentum distribution of decay products", 100, 0, 100);
h_momentum->SetXTitle("Momentum (GeV)");
h_momentum->SetYTitle("Number of Events");
```

## Simulating decay events

Monte Carlo Sampling:
- ```theta``` and ```phi```: The decay angles are generated randomly, following a uniform distribution. These represent the spherical coordinates in which the decay products are emitted.
- ```p```: The momentum of the decay products is sampled from a Gaussian distribution with a mean of 50 GeV and a standard deviation of 10 GeV, representing realistic physical uncertainties.

```TLorentzVector```:
- Represents a particle's 4-momentum (energy and 3-momentum).
- The momentum components px, py, pz are calculated from the polar and azimuthal angles.
- The energy of the product is assumed to be equal to its momentum (massless particles).

```
for (int i = 0; i < n_events; i++) {
    double theta = rand.Uniform(0, TMath::Pi()); // Polar angle from 0 to pi
    double phi = rand.Uniform(0, 2 * TMath::Pi()); // Azimuthal angle from 0 to 2*pi

    double p = rand.Gaus(50, 10); // Random momentum (Gaussian distribution around 50 GeV with a 10 GeV standard deviation)

    TLorentzVector product1;
    product1.SetPxPyPzE(p * sin(theta) * cos(phi), p * sin(theta) * sin(phi), p * cos(theta), p); // Assume massless decay products

    h_momentum->Fill(product1.P()); // Fill momentum histogram
}
```

## Fitting momentum distribution

Gaussian function has the form:

$$
f(x) = A \cdot e^{-\frac{(x - \mu)^2}{2\sigma^2}}
$$

where A is the amplitude, μ is the mean (center of the distribution), and σ is the standard deviation (width of the distribution).

Applying this function to the data and then using the fit command to fit the function to the histogram.

```
TF1* fit_function = new TF1("fit_function", "gaus", 0, 100);
fit_function->SetLineColor(kRed);
fit_function->SetLineWidth(2);

h_momentum->Fit("fit_function", "R");
```

## Visualising data

Now to make the code show up as a graph.

```
TCanvas* c1 = new TCanvas("c1", "Decay Simulation Results", 800, 600);
h_momentum->Draw("E");
fit_function->Draw("SAME");

TFile* output = new TFile("decay_simulation_results_momentum_fit.root", "RECREATE");
h_momentum->Write();
output->Close();
```

# Full Code

```
#include <iostream>
#include <TRandom3.h>
#include <TMath.h>
#include <TLorentzVector.h>
#include <TCanvas.h>
#include <TH1F.h>
#include <TFile.h>
#include <TF1.h>

void simulate_decay() {
    // Define constants
    double mass_parent = 125.0; // Mass of the decaying particle (e.g., Higgs) in GeV
    int n_events = 10000; // Number of events to simulate

    // Create a random number generator
    TRandom3 rand(123); // Seed value for reproducibility

    // Define histogram to store momentum distribution
    TH1F* h_momentum = new TH1F("h_momentum", "Momentum distribution of decay products", 100, 0, 100);

    // Set axis labels for the momentum histogram
    h_momentum->SetXTitle("Momentum (GeV)");
    h_momentum->SetYTitle("Number of Events");

    // Simulate n_events decay events
    for (int i = 0; i < n_events; i++) {
        // Generate random decay angle (uniform distribution for simplicity)
        double theta = rand.Uniform(0, TMath::Pi()); // Polar angle from 0 to pi
        double phi = rand.Uniform(0, 2 * TMath::Pi()); // Azimuthal angle from 0 to 2*pi

        // Calculate the 3-momentum of the decay products using random angle and energy conservation
        double p = rand.Gaus(50, 10); // Random momentum (Gaussian distribution around 50 GeV with a 10 GeV standard deviation)

        // Create four-vectors for the parent particle and decay products
        TLorentzVector product1;

        // Using the momentum to set the 4-momentum of the decay product
        product1.SetPxPyPzE(p * sin(theta) * cos(phi), p * sin(theta) * sin(phi), p * cos(theta), p); // Assume massless decay products

        // Analyze the results
        h_momentum->Fill(product1.P()); // Fill momentum histogram
    }

    // Create a Gaussian function to fit the momentum histogram
    TF1* fit_function = new TF1("fit_function", "gaus", 0, 100);
    fit_function->SetLineColor(kRed);
    fit_function->SetLineWidth(2);

    // Fit the momentum histogram
    h_momentum->Fit("fit_function", "R"); // "R" option to restrict fit to the range of the function

    // Create a canvas and draw the histogram with the fit
    TCanvas* c1 = new TCanvas("c1", "Decay Simulation Results", 800, 600);
    h_momentum->Draw("E"); // "E" to show error bars
    fit_function->Draw("SAME"); // Overlay the fit

    // Save the histogram and fit to a file
    TFile* output = new TFile("decay_simulation_results_momentum_fit.root", "RECREATE");
    h_momentum->Write();
    output->Close();
    
}
```
<img width="793" alt="Screenshot 2025-01-15 at 00 46 54" src="https://github.com/user-attachments/assets/3b215220-9d77-453d-9a90-ec87877aaf93" />


# Updated Code to showcase Angle Distribution of decay products and Energy Distribution of decay products - [NOT BEEN REFINED]

```
#include <iostream>
#include <TRandom3.h>
#include <TMath.h>
#include <TLorentzVector.h>
#include <TCanvas.h>
#include <TH1F.h>
#include <TFile.h>
#include <TF1.h>

void simulate_decay() {
    // Define constants
    double mass_parent = 125.0; // Mass of the decaying particle (e.g., Higgs) in GeV
    int n_events = 10000; // Number of events to simulate

    // Create a random number generator
    TRandom3 rand(123); // Seed value for reproducibility

    // Define histograms to store results
    TH1F* h_momentum = new TH1F("h_momentum", "Momentum distribution of decay products", 100, 0, 100);
    TH1F* h_angle = new TH1F("h_angle", "Angle distribution of decay products", 100, 0, TMath::Pi());
    TH1F* h_energy = new TH1F("h_energy", "Energy distribution of decay products", 100, 0, 100);

    // Set axis labels for the histograms
    h_momentum->SetXTitle("Momentum (GeV)");
    h_momentum->SetYTitle("Number of Events");

    h_angle->SetXTitle("Decay Angle (radians)");
    h_angle->SetYTitle("Number of Events");

    h_energy->SetXTitle("Energy (GeV)");
    h_energy->SetYTitle("Number of Events");

    // Simulate n_events decay events
    for (int i = 0; i < n_events; i++) {
        // Generate random decay angle (uniform distribution for simplicity)
        double theta = rand.Uniform(0, TMath::Pi()); // Polar angle from 0 to pi
        double phi = rand.Uniform(0, 2 * TMath::Pi()); // Azimuthal angle from 0 to 2*pi

        // Calculate the 3-momentum of the decay products using random angle and energy conservation
        double p = rand.Gaus(50, 10); // Random momentum (Gaussian distribution around 50 GeV with a 10 GeV standard deviation)

        // Create four-vectors for the parent particle and decay products
        TLorentzVector product1;

        // Using the momentum to set the 4-momentum of the decay product
        product1.SetPxPyPzE(p * sin(theta) * cos(phi), p * sin(theta) * sin(phi), p * cos(theta), p); // Assume massless decay products

        // Fill the histograms with the results
        h_momentum->Fill(product1.P());  // Fill momentum histogram
        h_angle->Fill(theta);            // Fill angle histogram
        h_energy->Fill(product1.E());    // Fill energy histogram
    }

    // Create a Gaussian function to fit the momentum histogram
    TF1* fit_function = new TF1("fit_function", "gaus", 0, 100);
    fit_function->SetLineColor(kRed);
    fit_function->SetLineWidth(2);

    // Fit the momentum histogram
    h_momentum->Fit("fit_function", "R"); // "R" option to restrict fit to the range of the function

    // Create a canvas and draw the histograms
    TCanvas* c1 = new TCanvas("c1", "Decay Simulation Results", 800, 600);
    c1->Divide(2, 2);  // Divide canvas into 2x2 grid
    c1->cd(1);  // First pad (top-left) for momentum distribution
    h_momentum->Draw("E");  // "E" to show error bars
    fit_function->Draw("SAME");  // Overlay the fit

    c1->cd(2);  // Second pad (top-right) for energy distribution
    h_energy->Draw();

    c1->cd(3);  // Third pad (bottom-left) for angle distribution
    h_angle->Draw();

    // Save the histograms and fit to a file
    TFile* output = new TFile("decay_simulation_results.root", "RECREATE");
    h_momentum->Write();
    h_angle->Write();
    h_energy->Write();
    output->Close();
}
```
<img width="797" alt="Screenshot 2025-01-15 at 00 49 18" src="https://github.com/user-attachments/assets/4f8692a1-586c-42b0-8867-097804ed87ff" />



