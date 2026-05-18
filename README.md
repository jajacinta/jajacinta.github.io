# Chicken Egg Colour Calculator

Punnett Square Calculator that will predict the possible chicken egg colours based on parental genotypes.

## Website
You can use the calculator live here: [https://jajacinta.github.io](https://jajacinta.github.io)

## How It Works
The calculator works based on two primary genetic traits:
Chickens can lay either white or blue base coloured shells and then a brown topcoat may be added on top.

* If a **white** egg has a brown topcoat, the egg will be **tan** or **brown**.
* If a **blue** egg has a brown topcoat, the egg will be **green** or **olive** (depending on the shade of tan/brown).

## Gene Breakdown
* **ww**: The White Shell gene (recessive). 
* **WW / Ww**: The Blue Shell gene (dominant).
* **BB / Bb**: The Brown Coating gene (determines the overlay strength for green, olive, tan, or brown shades).
* **CC / cc**: Optional third modifier gene to add deeper brown shade variety to the eggs
Input a 2, 4 or 6-letter genotypes for Parent 1 and Parent 2 to instantly generate a 2x2, 4x4 or 8x8 Punnett Square, visual egg colour map and exact probability statistics.

## Built With
* **HTML5**
* **CSS3** (Custom Dark Mode Interface)
* **Vanilla JavaScript** (Genotype permutation engine)
