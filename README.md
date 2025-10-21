import json
import os
import time

SAVE_FILE = "study_game_save.json"

def load_game():
    if os.path.exists(SAVE_FILE):
        with open(SAVE_FILE, "r") as f:
            return json.load(f)
    return {
        "points": 0,
        "points_per_study": 1,
        "upgrades": {
            "Notebook": 0,
            "Coffee": 0,
            "Study Lamp": 0
        }
    }

def save_game(data):
    with open(SAVE_FILE, "w") as f:
        json.dump(data, f, indent=4)

def show_status(data):
    print("\n📘 Current Status:")
    print(f"   Study Points: {data['points']}")
    print(f"   Points per Study: {data['points_per_study']}")
    print(f"   Upgrades:")
    for item, level in data['upgrades'].items():
        print(f"     - {item}: Level {level}")
    print()

def upgrade_menu(data):
    upgrades = {
        "1": ("Notebook", 50, 1),
        "2": ("Coffee", 200, 3),
        "3": ("Study Lamp", 500, 7)
    }
    print("\n🛒 Upgrade Menu:")
    for key, (name, cost, power) in upgrades.items():
        print(f" {key}. {name} (Cost: {cost} points) - Adds +{power} points/study")

    choice = input("Select upgrade (or press Enter to cancel): ").strip()
    if choice in upgrades:
        name, cost, power = upgrades[choice]
        if data['points'] >= cost:
            data['points'] -= cost
            data['points_per_study'] += power
            data['upgrades'][name] += 1
            print(f"✅ Upgraded {name} to Level {data['upgrades'][name]}!")
        else:
            print("❌ Not enough study points!")
    else:
        print("🔙 Returning to study...")

def main():
    print("🎓 Welcome to 'Study and Be Fun'!")
    print("Press [Enter] to Study. Type 'upgrade' to buy upgrades. Type 'exit' to save and quit.\n")

    data = load_game()
    show_status(data)

    while True:
        cmd = input(">> ").strip().lower()
        
        if cmd == "":
            data['points'] += data['points_per_study']
            print(f"📝 You studied and earned {data['points_per_study']} points! Total: {data['points']}")
        elif cmd == "upgrade":
            upgrade_menu(data)
        elif cmd == "exit":
            save_game(data)
            print("💾 Progress saved. Goodbye!")
            break
        else:
            print("❓ Unknown command. Press Enter to study, or type 'upgrade' or 'exit'.")

        # Autosave every loop
        save_game(data)

if __name__ == "__main__":
    main()
