# Project: MiniChem C++ Library with Python Bindings

## Overview
A lightweight C++ graph-based cheminformatics core exposed to Python via `pybind11` and built using `CMake`.

## Build System & Technical Stack
- **Language:** C++17 & Python 3.10+
- **Build System:** `CMakeLists.txt` configuring a C++ shared object library and `pybind11` module named `minichem`.
- **Dependencies:** C++ Standard Library (`std::vector`, `std::string`, `std::unordered_map`) and `pybind11`. No external cheminformatics libraries (RDKit, OpenBabel).

## Core Data Structures & Graph Representation
- **Atom Struct:** Index, element symbol (`C`, `N`, `O`, `H`, etc.), 3D coordinates `(x, y, z)`, explicit hydrogen count.
- **Bond Struct:** Source atom index, target atom index, bond order (1 for Single, 2 for Double, 3 for Triple).
- **Molecule Class:**
  - Graph representation using adjacency lists.
  - Operations:
    - `add_atom(symbol, x, y, z)` -> returns atom index.
    - `add_bond(atom1_idx, atom2_idx, order)` -> adds undirected edge.
    - `set_bond_order(atom1_idx, atom2_idx, new_order)` -> updates existing edge.
    - `get_smiles()` -> exports simplified linear SMILES string.

## I/O Specifications
- **XYZ File Format:** 
  - Read/Write `.xyz` files (Line 1: atom count, Line 2: comment/title, Lines 3+: `Symbol X Y Z`).
- **Simplified SMILES Generator/Parser:**
  - Support simple unbranched/branched chains for non-hydrogen organic elements (`C`, `N`, `O`) and explicit bond symbols (`-`, `=`).

## Molecular Descriptors
- `molecular_weight()`: Sum of atomic masses for all atoms in the graph.
- `num_hbd()`: Count of hydrogen bond donors (N or O atoms attached to at least 1 Hydrogen).
- `num_hba()`: Count of hydrogen bond acceptors (N or O atoms).

## Fingerprints & Similarity Searching
- **Fingerprint:** Generate a fixed **1024-bit topological fingerprint** (`std::bitset<1024>` or `std::vector<bool>`).
  - Algorithm: Traverse all paths of length 0 to 3 in the molecular graph (e.g., atom types + bond orders along the path).
  - Hash each path to an integer and set bit `hash % 1024 = 1`.
- **Tanimoto Similarity:** Compute bitwise Tanimoto coefficient on bitsets A and B.
- **Similarity Search Function:** `similarity_search(query_mol, target_mols, threshold)` returning molecules with Tanimoto similarity >= threshold.

## Testing Matrix (Python `pytest`)
1. **XYZ Coordinate Fidelity:** Read a sample XYZ string/file, modify atom 0 position, write to temporary file, read back, assert coordinates match floating-point values.
2. **Bond Editing:** Construct `CC=CC` in memory, update double bond to single bond (`set_bond_order`), export SMILES, assert result equals `CCCC`.
3. **Atom Addition:** Construct `CCC` in memory, add carbon with double bond to the terminal carbon, export SMILES, assert result equals `CCC=C`.
4. **Descriptors Test:** Construct a molecule with formula $C_2H_5OH$ (Ethanol):
   - Molecular weight ~46.07 g/mol
   - H-Bond Donors = 1
   - H-Bond Acceptors = 1
5. **Similarity Search:** Search a query molecule against a list of 5 target molecules and verify the correct target exceeds the similarity threshold.