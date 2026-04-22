This README file contains the detailed information on the functions used in the parametrization and translation programs being developed. The MATLAB programs are as follows:

1. CRITERIA (Computing paRametrized posITive EquilibRIA) - a MATLAB program used to obtain an equilibrium parametrization of a given reaction network
2. REWARDZ (tRanslation via Elementary flux modes towards WeAkly Reversible Deficiency Zero network) - a MATLAB program used to construct a weakly reversible and deficiency zero translation of a given reaction network. This has two versions: 1) one that first breaks down the network into its finest independent subnetworks before performing the translation procedure, and 2) one that performs the translation procedure to the whole network as is.

# CRITERIA (Computing paRametrized posITive EquilibRIA)

Main function: ComputeEquilibria

The function ComputeEquilibria returns the analytic equilibria of a chemical reaction network (CRN) parametrized by rate constants and free parameters (sigma), if any. The system's conservation laws are also listed after the solution. The output variables 'equation', 'species', 'free_parameter', 'conservation_law', and 'model' allow the user to view the following, respectively:                

        - List of parametrization of the steady state of the system         
        - List of steady state species of the network                       
        - List of free parameters of the steady state                       
        - List of conservation laws of the system                          
        - Complete network with all the species listed in the 'species' field of the structure 'model'    
                             
Finally, the elapsed time for producing the output is also displayed using MATLAB's built-in tic and toc functions. 

INPUT - model: a structure representing the CRN (see below for the details on how to fill out the structure)

# REWARDZ (tRanslation via Elementary flux modes towards WeAkly Reversible Deficiency Zero network)

This MATLAB program has two versions. 

The first one incorporates network decomposition in the procedure. That is, it first breaks down the network into its finest independent subnetworks before performing the translation method. The second version, on the other hand, performs the translation procedure to the whole network as is.

## First version

Main function: TranslationProgramWithDecomp

The function TranslationProgramWithDecomp returns a weakly reversible and deficiency zero translation of a given CRN using the elementary flux mode-based method as outlined in the main paper. Independent network decomposition is incorporated into the implementation of the procedure. The output variables 'N', 'R', 'x', 'translatedSource', 'translatedProduct', and 'Index_all' allow the user to view the following, respectively:

        - Stoichiometric matrix of the network         
        - Set of elementary flux modes                       
        - Binary linear program solution in the construction of the reaction-to-reaction graph                       
        - Matrix of translated source complexes                          
        - Matrix of translated product complexes
        - Array mapping translated reactions back to their corresponding original reaction indices    
                             
Finally, the elapsed time for producing the output is also displayed using MATLAB's built-in tic and toc functions. 

INPUT - model: a structure representing the CRN (see below for the details on how to fill out the structure)

## Second version

Main function: TranslationProgram

The function TranslationProgram returns a weakly reversible and deficiency zero translation of a given CRN using the elementary flux mode-based method as outlined in the main paper. The output variables 'N', 'R', 'x', 'translatedSource', and 'translatedProduct' allow the user to view the following, respectively:

        - Stoichiometric matrix of the network         
        - Set of elementary flux modes                       
        - Binary linear program solution in the construction of the reaction-to-reaction graph                       
        - Matrix of translated source complexes                          
        - Matrix of translated product complexes   
                             
Finally, the elapsed time for producing the output is also displayed using MATLAB's built-in tic and toc functions. 

INPUT - model: a structure representing the CRN (see below for the details on how to fill out the structure)

# Notes

     1. CRITERIA builds on COMPILES [1] by improving its performance. The following are the improvements made:
    	- The translation scheme of COMPILES is replaced by the elementary flux mode-based procedure.
    	- The workflow of the algorithm is tweaked. That is, the translated subnetworks are first merged before solving the analytic equilibria. This avoids the merging of solutions per subnetwork.
    	- Additional conditions are introduced in the procedure to solve the analytic equilibria of CRN with positive kinetic deficiency.

     2. The assumption is that the CRN has mass action kinetics.

     3. The algorithm begins by decomposing the network into its finest independent decomposition. Subnetworks that are not weakly reversible or have positive deficiency are then translated using the elementary flux mode–based method. The translated subnetworks are subsequently merged by taking the union of their reaction sets to form a one whole network. Finally, the analytic equilibria of the translated network are computed.

     4. The function TranslationProgramWithDecomp is part of the workflow of CRITERIA. Inside TranslationProgramWithDecomp, the functions indepDecomp and TranslationProgram are called which decomposes the network into its finest independent subnetworks and performs the elementary flux mode-based translation, respectively. 

     5. REWARDZ is another MATLAB program which returns a weakly reversible and deficiency zero translation of a given CRN. There are three major steps in the translation method:
	- Compute the elementary flux modes of the CRN via the Canonical Basis Method (a variant of the Double Description Method) [2].
	- Construct the reaction-to-reaction graph via solving a binary linear program [3].
	- Determine the translation complexes via a graph traversal method [3].

     6. The function TranslationProgram implements REWARDZ. Another version of it is the one which incorporates network decomposition in the translation procedure. That is, the network is first decomposed into its finest independent subnetworks before performing network translation. This is handled by the function TranslationProgramWithDecomp. The function TranslationProgram directly performs the translation method to the whole network as is. 

     7. The function TranslationProgramWithDecomp calls TranslationProgram in its implementation.

     8. Decomposition into the finest independent decomposition comes from [4].

     9. The elementary flux mode-based translation method comes from [3].

     10. Parametrization of steady state solution comes from [5].

     11. Computation of directed spanning trees toward vertices come from [6].

     12. Make sure that the classes graph_, edge, and vertex are in the same folder/path being used as the current working directory.

# Filling out the 'model' structure

'model' is the input for the functions ComputeEquilibria, TranslationProgram, and TranslationProgramWithDecomp. It is a structure, representing the CRN, with the following fields:

   - id: name of the model
   - species: a list of all species in the network; this is left blank since incorporated into the function is a step which compiles all species used in the model
   - reaction: a list of all reactions in the network (make sure to list the reactions individually, that is, treat reversible reactions as separate reactions), each with the following subfields:
        - id: a string representing the reaction
        - reactant: has the following further subfields:
             - species: a list of strings representing the species in the reactant complex
             - stoichiometry: a list of numbers representing the stoichiometric coefficient of each species in the reactant complex (listed in the same order of the species)
        - product: has the following further subfields:
             - species: a list of strings representing the species in the product complex
             - stoichiometry: a list of numbers representing the stoichiometric coefficient of each species in the product complex (listed in the same order of the species)
        - reversible: set value to "false"
        - kinetic: has the following further subfields:
             - reactant1: a list of numbers representing the kinetic order of each species in the reactant complex in the left to right direction (listed in the same order of the species)
             - reactant2: leave blank

To fill out the 'model' structure, write a string for 'model.id': this is just to put a name to the network. To add the reactions to the network, use the function addReaction where the output is 'model'. addReaction is developed to make the input of reactions of the CRN easier than the input in [10]. The following are the inputs and outputs of the function addReaction:
   
      - OUTPUT: Returns a structure called 'model' with added field 'reaction' with subfields 'id', 'reactant', 'product', 'reversible', and 'kinetic'. The output variable 'model' allows the user to view the network with the added reaction.
      
      - INPUTS
           - model: a structure, representing the CRN
           - id: visual representation of the reaction, e.g., reactant -> product (string)
           - reactant_species: species of the reactant complex (cell)
           - reactant_stoichiometry: stoichiometry of the species of the reactant complex (cell)
           - reactant_kinetic: kinetic orders of the species of the reactant complex (array)
           - product_species: species of the product complex (cell)
           - product_stoichiometry: stoichiometry of the species of the product complex (cell)
           - product_kinetic: leave blank
           - reversible: logical; set value to "false"

      * Make sure the function addReaction is in the same folder/path being used as the current working directory.

# Examples

26 examples are included:

   - Model A: Histidine Kinase [7]

   - Model B: Miao's Influenza Virus Model [8]

   - Model C: IDHKP-IDH [9]

   - Model D: 1-Site PD Network [10]

   - Model E: Hybrid Histidine Kinase [7]

   - Model F: PTM System [11]

   - Model G: 2-Protein Gene Transcription [7,12]

   - Model H: Reduced ERK Network [13]

   - Model I: 2-Site PD Network [10]

   - Model J: Two-Substrate Phosphorylation [7]

   - Model K: Two-Layer Cascade of Modification Cycles [11]

   - Model L: EnvZ-OmpR [5]

   - Model M: Two-Component Osmoregulator in E. Coli [14]

   - Model N: Hernandez's Influenza Virus Model [8]

   - Model O: Modified Two-Component Osmoregulator in E. Coli [14]

   - Model P: CRISPRi Model 1 [15]

   - Model Q: MAPK [3]

   - Model R: Full ERK Model [13]

   - Model S: 3-Site PD Network [10]

   - Model T: Zigzag Model of Plant-Pathogen Interactions [3]

   - Model U: CRISPRi Model 2 [15]

   - Model V: MAPK Signaling Cascade [11]

   - Model W: WNT Signaling Pathway [5]

   - Model X: Insulin Signaling Pathway [16]

   - Model Y: CRISPRi Model 3 [15]

   - Model Z: CRISPRi Model 4 [15]

All models include checker files to show that the solutions returned by CRITERA are indeed steady states of their respective ordinary differential equations.

# Contact Information

For questions, comments, and suggestions, feel free to contact either of the following email addresses: 

    - evvillejo@up.edu.ph
    - exequieljun@gmail.com

# References

[1] Hernandez BS, Lubenia PVN, Johnston MD, Kim JK (2023) A framework for deriving analytic steady states of biochemical reaction networks. PLoS Comput Biol. 19(4):e1011039.

   [2] Klapper I, Szyld DB, Zhao K. Metabolic networks, elementary flux modes, and polyhedral cones Society for Industrial and Applied Mathematics; 2021.

   [3] Johnston MD, Burton E. Computing weakly reversible deficiency zero network translations using elementary flux modes Bull Math Biol. 2019;81:1613–1644.

   [4] Hernandez B, De la Cruz R (2021) Independent decompositions of chemical reaction networks. Bull Math Biol 83(76):1–23. https://doi.org/10.1007/s11538-021-00906-3

   [5] Johnston M, Mueller S, Pantea C (2019) A deficiency-based approach to parametrizing positive equilibria of biochemical reaction systems. Bull Math Biol 81:1143–1172. https://doi.org/10.1007/s11538-018-00562-0

   [6] Gabow H and Meyers E (1978) Finding all spanning trees of directed and undirected graphs. SIAM J Comput 7(3):280-287. https://doi.org/10.1137/0207024

   [7] Conradi C, Feliu E, Mincheva M, Wiuf C. Identifying parameter regions for multistationarity. PLOS Comp Bio. 2017; 13(10): e1005751.

   [8] Peter S, Holzer M, Lamkiewicz K, Speroni di Fenizio P, Al Hwaeer H, Marz M, Schuster S, Dittrich P, Ibrahim B. Structure and hierarchy of influenza virus models revealed by reaction network analysis Viruses. 2019;11(5):449.

   [9] Shinar G, Rabinowitz J, Alon U. Robustness in glyoxylate bypass regulation PLOS Comp Bio. 2009;5(3):e1000297.

   [10] Villareal KA, Hernandez BS, Lubenia PV. Derivation of steady state parametrizations of chemical reaction networks with n independent and identical subnetworks MATCH Commun Math Comput Chem. 2024;91:337-365.

   [11] Feliu E, Wiuf C. Variable elimination in post-translational modification reaction networks with mass-action kinetics J Math Biol. 2013;66:281-310.

   [12] Siegal-Gaskins D, Franco E, Zhou T, Murray R. An analytical approach to bistable biological circuit discrimination using real algebraic geometry J R S Interface. 2015;12:20150288.

   [13] Obatake N, Shiu A, Tang X, Torres A. Oscillations and bistability in a model of ERK regulation J Math Biol. 2019;79:1515-1549.

   [14] Karp R, Perez Millan M, Dasgupta T, Dickenstein A, Gunawardena J. Complex-linear invariants of biochemical networks J Theor Biol. 2012;311:130-138.

   [15] Santos-Moreno J, Tasiudi E, Stelling J, Schaerli Y. Multistable and dynamic CRISPRi-based synthetic circuits. Nat Commun. 2020;11:2746.

   [16] Lubenia PVN, Mendoza ER, Lao AR. Reaction Network Analysis of Metabolic Insulin Signaling. Bull Math Biol. 2022;84:129.
