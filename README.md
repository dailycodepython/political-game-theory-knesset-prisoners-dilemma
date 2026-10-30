# political-game-theory-knesset-prisoners-dilemma

# BibiBreak: The Cartel Cracker 
## 🎭 FaceSaving-Protocol: How to Buy a Coalition (Knesset 2026 Edition)

# BibiBreak: Coalition Game Theory Simulator (Knesset 2026 Edition)

Welcome to **BibiBreak** (or *The Kingmaker's Dilemma*), a Game Theory sandbox written in Python. This repository models the complex political deadlock of the 26th Israeli Knesset elections (October 2026) using quantitative game theory concepts, specifically the **Banzhaf Power Index** and **Nash Equilibria in strategic bargaining**.

---

## 📊 The Core Concept
The repository explores how political coalitions form when a traditional ruling giant party is removed from the equation. It mathematically simulates how an opposition block can break a unified religious cartel using an external disciplining threat (the asymmetric option of teaming up with Arab factions), turning a multi-party compromise into a textbook **Prisoner's Dilemma** where individual defection is the strictly dominant strategy.

---

## 🛠️ Features Included

* **Banzhaf Power Index Calculator:** Analyzes the exact percentage of "voting shares" (coalition weight) each faction holds under strict ideological restrictions.
* **Asymmetric Bargaining Game Simulator:** Models a matrix game showing why individual religious factions face a strict mathematical payoff of `0` if they attempt a collective boycott, forcing a collapse of their cartel into a cheap capitulation.
* **Valid Coalition Generator:** Programmatically loops through all combinations to output feasible governments meeting the 61-seat threshold.

---

## 💻 Script Code (`coalition_simulator.py`)

Here is the source code engineered for this project. Save this as `coalition_simulator.py` to run calculations locally.

```python
from itertools import combinations

def analyze_knesset_game():
    # 2026 Knesset Seat Distribution (Excluding Likud's 27 seats)
    parties = {
        "Yashar_Core": 21,       # Opposition Main Hub
        "Be_Yahad": 12,          # Centrist Allies
        "Democrats": 8,          # Left-wing
        "Liberman_NDI": 7,       # Secular Right
        "Arab_Raam": 8,          # Moderate Arab Faction (Mansour Abbas)
        "Utj": 7,                # Ultra-Orthodox 1 (United Torah Judaism)
        "Shas": 6,               # Ultra-Orthodox 2
        "Otzma": 7,              # Far-Right (Ben-Gvir)
        "Smotrich": 6,           # Far-Right (Religious Zionism)
        "Hadash_Taal": 11        # Arab List (Always sits out of coalitions)
    }

    threshold = 61
    party_names = list(parties.keys())

    # Constraints verification
    def is_valid(subset):
        if "Hadash_Taal" in subset: return False
        if "Arab_Raam" in subset and ("Smotrich" in subset or "Otzma" in subset):
            return False
        return True

    # 1. Banzhaf Power Index Calculations
    critical_defections = {name: 0 for name in parties}
    valid_combos = []

    for r in range(1, len(party_names) + 1):
        for subset in combinations(party_names, r):
            if not is_valid(subset): continue
            if sum(parties[p] for p in subset) >= threshold:
                valid_combos.append(subset)
                for p in subset:
                    reduced = [x for x in subset if x != p]
                    if sum(parties[x] for x in reduced) < threshold or not is_valid(reduced):
                        critical_defections[p] += 1

    total_crit = sum(critical_defections.values())

    print("=== BANZHAF VOTING POWER SHARES ===")
    for party, score in sorted(critical_defections.items(), key=lambda x: x, reverse=True):
        weight = (score / total_crit) * 100 if total_crit > 0 else 0
        print(f"-> {party:<15}: {parties[party]:<2} Seats | Share: {weight:.2f}%")

    # 2. Key 5-Party Cohesive Coalition Variants
    print("\n=== MINIMAL FEASIBLE COALITIONS (WITHOUT LIKUD) ===")
    sorted_combos = sorted(valid_combos, key=lambda x: sum(parties[p] for p in x))
    
    counter = 1
    for combo in sorted_combos:
        if len(combo) <= 6 and "Smotrich" not in combo and "Otzma" not in combo:
            print(f"Variant #{counter:02d} (Seats: {sum(parties[p] for p in combo)}): {list(combo)}")
            counter += 1
            if counter > 5: break

    # 3. Bargaining Game Payoff Matrix (The Cartel Cracker)
    print("\n=== PRISONER'S DILEMMA MATRIX FOR THE ORTHODOX CARTEL ===")
    print("         UTJ: Stand Together | UTJ: Defect Separately")
    print("SHAS: Union     (0.0, 0.0)    |     (0.0, 8.0)")
    print("SHAS: Defect    (8.0, 0.0)    |     (2.0, 2.0) <-- Nash Equilibrium")
    print("\nVerdict: Because staying united results in a payoff of 0 (as the opposition can ")
    print("substitute with Arab_Raam), both parties possess a strictly dominant strategy to ")
    print("defect. The cartel shatters, and the opposition secures them at a discount price.")

if __name__ == '__main__':
    analyze_knesset_game()
```

---

## 📈 Theoretical Backing

### The Ideological "Face-Saving" Variable
This simulation models an ideal political framing scenario. By leveraging the credible alternative threat of an `Arab_Raam` alliance, the opposition drives a wedge through the orthodox faction block (`Shas` + `Utj`). 

Once the cartel breaks and the religious parties capitulate, they carry home an airtight narrative for their voter base: *"We did not defect for budgets; we selflessly joined the cabinet to exclude Arab parties from the coalition and protect the state's Jewish character."* This creates an incredibly stable Nash equilibrium where both entry cost is reduced, and future exit/blackmail threats are muted due to an ever-present backup partner standing outside.
