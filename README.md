# Energy Market Simulator

Simulation des marchés de l'énergie européens — prix spot, forward, commodités (gaz, CO₂).

## Caractéristiques

- **Merit order dynamique** : formation du prix spot heure par heure
- **Commodités stochastiques** : TTF (gaz) et EUA (CO₂) en Ornstein-Uhlenbeck
- **Profils réalistes** : demande saisonnière, ENR (éolien + solaire) avec variabilité
- **Marché forward** : courbe de prix à terme et calcul de la prime de risque
- **Visualisations** : 8 panneaux dashboard (spot, mix, forward, etc.)

## Structure

```
energy-market-sim/
├── src/                    # Modules principaux
│   ├── config.py          # Paramètres (capacités, coûts, etc.)
│   ├── commodities.py     # Processus stochastiques TTF & EUA
│   ├── demand.py          # Modèles de demande et ENR
│   ├── merit_order.py     # Formation du prix spot
│   ├── forward_market.py  # Courbe forward & prix contrats
│   └── simulation.py      # Orchestration + visualisations
├── scripts/               # Runners
│   └── run_full_simulation.sh
├── outputs/               # Résultats (images, CSVs)
├── tests/                 # Tests unitaires
├── main.py               # Entrypoint
├── requirements.txt      # Dépendances
├── .gitignore
└── README.md
```

## Installation

### Prérequis
- Python 3.9+
- pip

### Setup rapide

```bash
git clone <ton-repo>
cd energy-market-sim

# Installer dépendances
pip install -r requirements.txt

# Lancer la simulation
python main.py

# Ou via le script bash
bash scripts/run_full_simulation.sh
```

## Usage

### Mode interactif

```python
from src.simulation import run_simulation

results = run_simulation()

# Accéder aux résultats
spot_prices = results["spot"]
ttf_prices = results["ttf"]
dispatch = results["dispatch"]  # production par technologie
```

### Mode CLI

```bash
# Run the full simulation (8760 heures)
python main.py

# Résultat : energy_market_simulation.png dans ./outputs/
```

## Outputs

La simulation génère :

- **energy_market_simulation.png** : dashboard 8 panneaux
  - Prix spot annuel (€/MWh)
  - Distribution des prix
  - Trajectoire TTF & EUA
  - Mix de production (semaine hiver + été)
  - Coûts marginaux vs spot
  - Courbe forward
  - ENR & demande résiduelle

## Calibration & paramètres

Tous les paramètres sont dans `src/config.py` :

```python
CAPACITIES = {
    "nuclear": 63_000,    # MW
    "wind": 22_000,
    ...
}

DEMAND = {
    "base": 45_000,       # MW
    "amplitude": 20_000,  # saisonnalité
    "noise_std": 2_000,   # bruit
}

TTF = {
    "mu": 35.0,           # niveau d'équilibre €/MWh_gaz
    "kappa": 2.0,         # vitesse mean-reversion
    "sigma": 8.0,         # volatilité
    "S0": 38.0,           # prix initial
}
```

Modifie ces valeurs pour explorer différents scénarios.

## Résultats type (France 2023-like)

| Indicateur | Valeur |
|---|---|
| TTF moyen | ~34 €/MWh_gaz |
| EUA moyen | ~58 €/tCO₂ |
| **Spot moyen** | ~17 €/MWh |
| Baseload Cal | ~17 €/MWh |
| Peakload Cal | ~23 €/MWh |
| Peak/base spread | ~6 €/MWh |
| Heures négatifs | ~0% |

## Roadmap

- [ ] Scénarios chocs (TTF ×3, nucléaire -20%, etc.)
- [ ] Export CSV complet (hourly spot, dispatch, etc.)
- [ ] Pricer de contrats clients (swing, take-or-pay, etc.)
- [ ] Monte Carlo : N simulations → distributions
- [ ] Tests unitaires (merit order, forward pricing)
- [ ] Calibration automatique vs données réelles EPEX

## Contribuer

1. Fork le repo
2. Crée une branche (`git checkout -b feature/amélioration`)
3. Commit tes changes (`git commit -m "Add feature"`)
4. Push (`git push origin feature/amélioration`)
5. Ouvre une Pull Request

## License

MIT

## Auteur

Brieuc — IMT Nord Europe, EDF Stage 2026
