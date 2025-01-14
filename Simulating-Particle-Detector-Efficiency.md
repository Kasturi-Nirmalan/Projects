# Particle Detection Efficiency Simulation

This project simulates particle detection efficiency by randomly generating particle energies and determining whether each particle is detected based on a specified energy threshold. The code tracks how many particles are detected and how many are not, and then calculates the detection efficiency as a percentage. It will then make a histogram of the detected and undetected particles.

The following sections describe how the code works in chunks and the steps involved in simulating particle detection.

---

## Generating Random Particle Energies

In each simulated event, a random energy between 0 and 200 GeV is generated to represent a particle's energy. The `TRandom` class in ROOT is used to generate this energy.

```cpp
TRandom randGen;  // Random number generator
double energy = randGen.Uniform(0, 200);  // Generate a random energy between 0 and 200 GeV
```
## Defining the energy threshold

Particles are detected based on their energy. If the particle's energy is greater than or equal to a specified detection threshold (100 GeV), the particle is considered detected. Otherwise, it is not detected.

```
bool isDetected(double energy, double threshold) {
    return energy >= threshold;  // Particle detected if energy is greater than or equal to the threshold
}
```

## Simulating multiple events

I have made it so that there will be a 1000 simulated events, each of them being a different particle with their own corresponding energy which is randonly generated for them. For each event, the code will check whether the particle will fall under the threshold (not detected) or above the threshold (detected). It will then put them in their corresponding bins.

```
int nEvents = 1000;  // Number of events to simulate

int detectedCount = 0;
int notDetectedCount = 0;

for (int i = 0; i < nEvents; i++) {
    double energy = randGen.Uniform(0, 200);  // Generate a random energy

    bool detected = isDetected(energy, detectionThreshold);  // Check detection status
    
    if (detected) {
        detectedCount++;
        hist->Fill(1);  // Fill the "Detected" bin (index 1)
    } else {
        notDetectedCount++;
        hist->Fill(0);  // Fill the "Not Detected" bin (index 0)
    }
}
```

## Storing results into a histogram

I use a ROOT histogram to store the results of the simulation. I have made two bins, which are:
- Bin 1: Particles that are not detected (energy < 50 GeV)
- Bin 2: Particles that are detected (energy >= 50 GeV)

```
TH1F *hist = new TH1F("detected", "Detection Efficiency", 2, 0, 2);
hist->GetXaxis()->SetBinLabel(1, "Not Detected\n(Energy < 50 GeV)");
hist->GetXaxis()->SetBinLabel(2, "Detected\n(Energy >= 50 GeV)");

hist->Fill(detected ? 1 : 0);  // Fill the appropriate bin
```

## Visualising the results

The results are plotted on a ROOT canvas to visualize the detection efficiency. The x-axis represents the detection status (detected vs. not detected), and the y-axis represents the number of particles.

```
TCanvas *canvas = new TCanvas("canvas", "Detection Efficiency", 800, 600);
hist->SetFillColor(kBlue);  // Set the color of the bars
hist->Draw();  // Draw the histogram
```

## Calculating and displaying efficiency

The detection efficiency is calculated as the ratio of detected particles to total events, and the result is displayed as a percentage. Underneath is the formula I used to get the percentage.

$$ \text{Efficiency} = \frac{\text{Detected Count}}{\text{Total Events}} \times 100 $$

This will be displayed in the terminal.

```
double efficiency = (double)detectedCount / nEvents * 100;  // Efficiency in percentage
std::cout << "Detection Efficiency: " << efficiency << "%" << std::endl;
```

# Final Code
```
#include "TMath.h"
#include "TRandom.h"
#include "TH1F.h"
#include "TFile.h"
#include "TCanvas.h"
#include <iostream>

// Function to simulate detection based on energy threshold
bool isDetected(double energy, double threshold) {
    return energy >= threshold;  // Energy greater than or equal to threshold is detected
}

// Function to simulate particle detection efficiency and store results in a histogram
void analyseEfficiency() {
    TH1F *hist = new TH1F("detected", "Detection Efficiency", 2, 0, 2);
    hist->GetXaxis()->SetBinLabel(1, "Not Detected\n(Energy < 50 GeV)");
    hist->GetXaxis()->SetBinLabel(2, "Detected\n(Energy >= 50 GeV)");

    TRandom randGen;  // Random number generator
    int nEvents = 1000;  // Number of events to simulate
    double detectionThreshold = 100.0;  // Minimum energy required for detection (increased threshold)

    int detectedCount = 0;
    int notDetectedCount = 0;

    for (int i = 0; i < nEvents; i++) {
        double energy = randGen.Uniform(0, 200);  // Generate a random energy
        bool detected = isDetected(energy, detectionThreshold);  // Check detection status

        if (detected) {
            detectedCount++;
            hist->Fill(1);  // Fill the "Detected" bin
        } else {
            notDetectedCount++;
            hist->Fill(0);  // Fill the "Not Detected" bin
        }
    }

    // Create a ROOT file to save the histogram
    TFile *file = new TFile("efficiency.root", "RECREATE");
    hist->Write();
    file->Close();

    // Visualize the histogram
    TCanvas *canvas = new TCanvas("canvas", "Detection Efficiency", 800, 600);
    hist->SetFillColor(kBlue);  // Set the color of the bars
    hist->Draw();

    // Calculate and print the detection efficiency
    double efficiency = (double)detectedCount / nEvents * 100;
    std::cout << "Detection Efficiency: " << efficiency << "%" << std::endl;
}
```


<img width="796" alt="Screenshot 2025-01-14 at 23 55 22" src="https://github.com/user-attachments/assets/fb65af2d-d1ba-44cc-a2fd-331726e34ff6" />

<img width="196" alt="Screenshot 2025-01-14 at 23 57 01" src="https://github.com/user-attachments/assets/a20b5dab-235d-46cd-9397-5234f7f3815a" />

## Debugging Code

This was the code that I used to help me debug for errors. 

1. Prints the energy level of every 100 events to make sure the energy were being randomly distributed.
2. Prints the total events that were detected and not detected to check if the efficiency percentage was correct.

```
#include "TMath.h"
#include "TRandom.h"
#include "TH1F.h"
#include "TFile.h"
#include "TCanvas.h"
#include <iostream>

// Function to simulate detection based on energy threshold
bool isDetected(double energy, double threshold) {
    return energy >= threshold;  // Energy greater than or equal to threshold is detected
}

// Function to simulate particle detection efficiency and store results in a histogram
void analyseEfficiency() {
    // Create a histogram to store detection results: 0 for not detected, 1 for detected
    TH1F *hist = new TH1F("detected", "Detection Efficiency", 2, 0, 2);
    hist->GetXaxis()->SetBinLabel(1, "Not Detected\n(Energy < 50 GeV)");
    hist->GetXaxis()->SetBinLabel(2, "Detected\n(Energy >= 50 GeV)");

    // Simulate multiple events (e.g., 1000 events)
    TRandom randGen;  // Random number generator
    int nEvents = 1000;  // Number of events to simulate
    double detectionThreshold = 50.0;  // Minimum energy required for detection (increased threshold)

    int detectedCount = 0;
    int notDetectedCount = 0;

    for (int i = 0; i < nEvents; i++) {
        // Generate a random energy between 0 and 200 GeV
        double energy = randGen.Uniform(0, 200);

        // Debugging: Print the energy for every 100th event
        if (i % 100 == 0) { // Print energy for every 100th event
            std::cout << "Energy of event " << i << ": " << energy << " GeV" << std::endl;
        }

        // Check if the particle is detected based on the energy
        bool detected = isDetected(energy, detectionThreshold);
        
        // Increment count for detected particles
        if (detected) {
            detectedCount++;
            hist->Fill(1);  // Fill the "Detected" bin (index 1)
        } else {
            notDetectedCount++;
            hist->Fill(0);  // Fill the "Not Detected" bin (index 0)
        }
    }

    // Debugging: Print the counts of detected and undetected particles
    std::cout << "Detected Count: " << detectedCount << std::endl;
    std::cout << "Not Detected Count: " << notDetectedCount << std::endl;

    // Create a ROOT file to save the histogram
    TFile *file = new TFile("efficiency.root", "RECREATE");
    hist->Write();  // Save the histogram to the ROOT file
    file->Close();  // Close the file

    // Visualize the histogram
    TCanvas *canvas = new TCanvas("canvas", "Detection Efficiency", 800, 600);
    hist->SetFillColor(kBlue);  // Set the color of the bars
    hist->Draw();  // Draw the histogram

    // Customize axis labels
    hist->GetXaxis()->SetTitle("Detection Status (Energy Threshold)");  // X-axis title
    hist->GetYaxis()->SetTitle("Number of Events");  // Y-axis title

    // Set Y-axis to start from 0 (ensure the range is correct)
    hist->GetYaxis()->SetRangeUser(0, hist->GetMaximum() * 1.1);  // Adjust to start from 0

    // Display the efficiency result
    double efficiency = (double)detectedCount / nEvents * 100;  // Efficiency in percentage
    std::cout << "Detection Efficiency: " << efficiency << "%" << std::endl;
}
```
<img width="231" alt="Screenshot 2025-01-14 at 23 57 50" src="https://github.com/user-attachments/assets/384926ad-7a5e-477f-a05d-2eeb33e1e5a5" />


