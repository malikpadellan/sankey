# iCHO3K Model - Google Colab Simulation

This repository contains a Google Colab notebook for running the **iCHO3K genome-scale metabolic model** for Chinese Hamster Ovary (CHO) cells with advanced batch simulation and metabolite combination analysis capabilities.

## Features

### 1. Batch Culture Simulation
- Simulate CHO cell batch cultures over **customizable time periods** (days)
- Dynamic modeling of:
  - Cell growth (biomass accumulation)
  - Nutrient consumption (glucose, glutamine, etc.)
  - Metabolite production (lactate, etc.)
  - Growth rate changes over time
- Configurable parameters:
  - Initial biomass concentration
  - Initial metabolite concentrations
  - Culture volume
  - Time step resolution
  - Maximum uptake rates

### 2. Metabolite Combination Analysis
- Systematic testing of **metabolite combinations** and their effects on growth
- Support for:
  - Pairwise combinations
  - Triplet combinations
  - Higher-order combinations
  - Supplement testing with base medium
- Automated ranking by growth rate
- Identification of essential vs. beneficial metabolites

### 3. Comparative Analysis
- Compare multiple culture conditions side-by-side
- Test different supplementation strategies
- Visualize differences in:
  - Biomass growth trajectories
  - Nutrient consumption patterns
  - Metabolite production profiles
  - Growth rate dynamics

## Quick Start

### Option 1: Open in Google Colab

1. Upload `iCHO3K_Colab_Simulation.ipynb` to Google Colab
2. Or use this link (replace with your file URL):
   ```
   https://colab.research.google.com/github/YOUR_USERNAME/YOUR_REPO/blob/main/iCHO3K_Colab_Simulation.ipynb
   ```

### Option 2: Run Locally

1. Install dependencies:
   ```bash
   pip install cobra python-libsbml optlang swiglpk pandas numpy scipy matplotlib seaborn tqdm openpyxl jupyterlab
   ```

2. Clone the iCHO3K repository:
   ```bash
   git clone https://github.com/LewisLabUCSD/iCHO3K.git
   ```

3. Open the notebook:
   ```bash
   jupyter lab iCHO3K_Colab_Simulation.ipynb
   ```

## Usage Examples

### Running a Batch Simulation

```python
# Run a 7-day batch culture simulation
batch_results = simulate_batch_culture(
    model=model,
    days=7,                      # Simulation duration
    initial_biomass=0.2,         # g/L
    initial_glucose=25.0,        # mM
    initial_glutamine=4.0,       # mM
    timestep=1.0                 # hours
)

# Visualize results
plot_batch_results(batch_results)
```

### Testing Metabolite Combinations

```python
# Define metabolites to test
test_metabolites = {
    'Glucose': ('EX_glc__D_e', -10.0),
    'Glutamine': ('EX_gln__L_e', -2.0),
    'Serine': ('EX_ser__L_e', -0.5),
    'Glycine': ('EX_gly_e', -0.5),
}

# Test all pairwise combinations
pair_results = analyze_metabolite_combinations(
    model=model,
    metabolites_dict=test_metabolites,
    combination_size=2
)

# View top combinations
print(pair_results.head(10))
```

### Comparing Different Conditions

```python
# Define culture conditions
conditions = {
    'Base': {
        'EX_glc__D_e': (-10, 0),
        'EX_gln__L_e': (-2, 0),
    },
    'High Glucose': {
        'EX_glc__D_e': (-15, 0),
        'EX_gln__L_e': (-2, 0),
    },
}

# Run simulations for each condition
for name, bounds in conditions.items():
    with model:
        for rxn_id, bound in bounds.items():
            model.reactions.get_by_id(rxn_id).bounds = bound
        results = simulate_batch_culture(model, days=5)
```

## Notebook Structure

1. **Setup and Installation** - Install required packages
2. **Clone Repository** - Download iCHO3K model files
3. **Import Libraries** - Load necessary Python libraries
4. **Load Model** - Load and configure the iCHO3K model
5. **Test Model** - Verify basic functionality
6. **Batch Simulation** - Time-course culture simulations
7. **Metabolite Combinations** - Systematic combination testing
8. **Condition Comparison** - Compare multiple scenarios
9. **Export Results** - Save data to CSV files
10. **Custom Analysis** - Template for your own experiments

## Key Functions

### `simulate_batch_culture()`
Simulates a batch culture over multiple days with dynamic metabolite tracking.

**Parameters:**
- `model`: The iCHO3K COBRApy model
- `days`: Number of days to simulate (default: 7)
- `initial_biomass`: Initial biomass concentration in g/L (default: 0.1)
- `initial_glucose`: Initial glucose concentration in mM (default: 25.0)
- `initial_glutamine`: Initial glutamine concentration in mM (default: 4.0)
- `volume`: Culture volume in L (default: 1.0)
- `timestep`: Time step in hours (default: 1.0)

**Returns:** DataFrame with time-course data

### `analyze_metabolite_combinations()`
Tests all combinations of specified metabolites.

**Parameters:**
- `model`: The iCHO3K COBRApy model
- `metabolites_dict`: Dictionary of metabolites and their exchange reactions
- `combination_size`: Size of combinations to test (2 for pairs, 3 for triplets, etc.)
- `base_metabolites`: Optional base metabolites always present

**Returns:** DataFrame with growth rates for each combination

## Model Information

### iCHO3K Model Statistics
- **11,004 reactions**
- **7,377 metabolites**
- **3,597 genes**
- **3,489 mapped protein structures**

### Available Biomass Objectives
- `biomass_cho`: Generic CHO cells
- `biomass_cho_s`: CHO-S variant
- `biomass_cho_prod`: Production CHO cells (default)

### Common Exchange Reactions
- `EX_glc__D_e`: Glucose
- `EX_gln__L_e`: Glutamine
- `EX_lac__L_e`: Lactate
- `EX_o2_e`: Oxygen
- `EX_ser__L_e`: Serine
- `EX_gly_e`: Glycine
- `EX_pyr_e`: Pyruvate

## Outputs

The notebook generates:

1. **CSV Files**:
   - `batch_simulation_results.csv` - Time-course data
   - `metabolite_pair_results.csv` - Pairwise combination results
   - `metabolite_triplet_results.csv` - Triplet combination results
   - `batch_condition_*.csv` - Results for each tested condition

2. **Visualizations**:
   - Biomass growth curves
   - Nutrient consumption profiles
   - Metabolite production trajectories
   - Growth rate dynamics
   - Combination analysis bar charts
   - Growth rate distributions

## Customization

### Adding New Metabolites

```python
# Add to the test_metabolites dictionary
test_metabolites['Leucine'] = ('EX_leu__L_e', -0.3)  # uptake rate in mmol/gDW/h
```

### Adjusting Simulation Parameters

```python
# Modify any of these parameters
batch_results = simulate_batch_culture(
    model=model,
    days=10,              # Longer simulation
    initial_biomass=0.5,  # Higher starting density
    timestep=0.5          # Finer time resolution
)
```

### Testing Custom Conditions

```python
# Set specific exchange bounds
with model:
    model.reactions.EX_glc__D_e.bounds = (-12, 0)  # Custom glucose uptake
    model.reactions.EX_ser__L_e.bounds = (-1, 0)   # Add serine
    solution = model.optimize()
```

## Troubleshooting

### Installation Issues

If you encounter issues installing packages in Colab:
```python
!pip install --upgrade pip
!pip install cobra==0.29.0 python-libsbml swiglpk
```

### Model Loading Errors

If the model fails to load:
1. Ensure the iCHO3K repository is cloned
2. Check the model file path
3. Verify all dependencies are installed

### Optimization Failures

If FBA optimization fails:
1. Check metabolite bounds (ensure essential nutrients are available)
2. Verify oxygen is not limited
3. Try a different solver: `cobra.Configuration().solver = "gurobi"`

## Citation

If you use this notebook or the iCHO3K model in your research, please cite:

**iCHO3K Model:**
> Di Giusto et al. (2025). "iCHO3K: A community-driven, genome-scale metabolic reconstruction and analysis toolkit for Chinese Hamster Ovary cells." bioRxiv. https://doi.org/10.1101/2025.04.10.647063

**COBRApy:**
> Ebrahim A, et al. (2013). "COBRApy: COnstraints-Based Reconstruction and Analysis for Python." BMC Systems Biology. 7:74.

## Repository Structure

```
.
├── iCHO3K_Colab_Simulation.ipynb    # Main Google Colab notebook
├── README.md                         # This file
└── sankey.html                      # Visualization (if present)
```

## Additional Resources

- **iCHO3K GitHub**: https://github.com/LewisLabUCSD/iCHO3K
- **COBRApy Documentation**: https://cobrapy.readthedocs.io/
- **Flux Balance Analysis Tutorial**: https://opencobra.github.io/cobrapy/tutorials.html

## Support

For issues related to:
- **This notebook**: Open an issue in this repository
- **iCHO3K model**: Visit https://github.com/LewisLabUCSD/iCHO3K/issues
- **COBRApy**: Visit https://github.com/opencobra/cobrapy/issues

## License

This notebook is provided as-is for educational and research purposes. The iCHO3K model is subject to its original license terms.

## Author

Created for batch culture simulation and metabolite combination analysis of CHO cells using the iCHO3K genome-scale metabolic model.

---

**Happy modeling! 🧬🔬**
