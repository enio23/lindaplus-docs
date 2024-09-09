# Documentation
Here are provided documentation about the inputs that each main function that the LINDA+ R-package takes.

## Resources access
The resources that have been described in the [main page](https://enio23.github.io/lindaplus-docs/#resources), have been integrated into LINDA+ and combinations of such resources can be made readily available by running one of the following R commands:

* **Human**

```r
# DIGGER + DoRothEA + CellChat
load(file = system.file("extdata", "digger.dorothea.cellchat.human.RData", package = "LINDAPlus"))

# DIGGER + DoRothEA + CellPhoneDB
load(file = system.file("extdata", "digger.dorothea.cellphonedb.human.RData", package = "LINDAPlus"))

# DIGGER + DoRothEA + CellTalkDB
load(file = system.file("extdata", "digger.dorothea.celltalkdb.human.RData", package = "LINDAPlus"))

# DIGGER + DoRothEA + LIANA
load(file = system.file("extdata", "digger.dorothea.liana.human.RData", package = "LINDAPlus"))

# DIGGER + TfLink + CellChat
load(file = system.file("extdata", "digger.tflink.cellchat.human.RData", package = "LINDAPlus"))

# DIGGER + TfLink + CellPhoneDB
load(file = system.file("extdata", "digger.tflink.cellphonedb.human.RData", package = "LINDAPlus"))

# DIGGER + TfLink + CellTalkDB
load(file = system.file("extdata", "digger.tflink.celltalkdb.human.RData", package = "LINDAPlus"))

# DIGGER + TfLink + LIANA
load(file = system.file("extdata", "digger.tflink.liana.human.RData", package = "LINDAPlus"))

```

* **Mouse**
Not available yet

Each of the resources that can be loaded represents a data-frame with the following columns: 'pfam_source' (containing the PFAM ID of the source protein domain interactor), 'pfam_target' (containing the PFAM ID of the target protein domain interactor), 'gene_source' (containing the gene name of the protein of the source domain) and 'gene_target' (containing the gene name of the protein of the target domain). There are three types of interactions that can be distinguished in this data-frame:

- `PPI-DDI`: These are the joint domain-domain and protein-protein interactions that are used as a resource to model the interactions within each individual cell-type. They can be retreived from the data-frame object as those interactions whose both 'pfam_source' and 'pfam_target' elements are not NA's.
<div style="margin-top: 5px;"></div>
- `TF-Ligands`: These refer to those relations in which a transcription factor regulates the expression of a ligand gene which then undergoes transcriptional and translational processes before finally being released in the extra-cellular space. In this way, it would be possible for our models to help us understand through which cell-types and by which mechanism is a specific ligand secreted. They can be retreived from the data-frame object as those interactions whose both 'pfam_source' and 'pfam_target' elements are NA's.
<div style="margin-top: 5px;"></div>
- `Ligand-Receptors`: These refer to the ligand-receptor interactions. They can be retreived from the data-frame object as those interactions whose both 'pfam_source' values are NA's while 'pfam_target' elements are PFAM domain ID's corresponding to the extra-cellular domains which interact to ligands in the extra-cellular space.

## LINDA+ functionalities
The LINDA+ R-package implements two functions: **i)** `prepare_linda_inputs()`, which prepares the list of interaction tables that are used for the network inference; as well as **ii)** `runLINDAPlus()`, which is the main function of the package that is used to infer the regulatory inter- and intra- cellular protein interactions.

### _prepare_linda_inputs()_
Before performing any types of network inference analysis, users should first generate the _background.networks.list_ object that is taken as an input from the _runLINDAPlus()_ functionality by using the _prepare_linda_inputs()_ function. Besides generating the  _background.networks.list_, the _prepare_linda_inputs()_ can also optionally perform some processing of the provided interactions. Such processing steps consist in defining for example which genes to keep or to remove for each of the cell-types in their prior knowledge of interactions. For example, one instance when such processing step would be recommended is for the case when some genes might not be expressed in any given cell-type and thus this functionality would allow the users to remove protein nodes from the solution whose corresponding genes have not been expressed. In case when such a functionality is used, below is provided a description of the inputs that can be provided to such a function.

- `interactions_df`: A data-frame of all possible interactions (PPI/DDI, Ligand-Receptors and TF-to-Ligand) that will be used for the network inference analysis. The format of such an input should be the same as the ones described [above](https://enio23.github.io/lindaplus-docs/documentation/#resources-access).
<div style="margin-top: 5px;"></div>
- `cell_types`: Names of the cell-types. **Note:** Do not assign the 'LR' name to any of the cell-types as this is reserved by LINDA+ for it's internal network inference analysis.
<div style="margin-top: 5px;"></div>
-  `genes2keep`: An optional named list object (same as 'cell_types') of character vectors defining the list of gene-names to keep in the interaction knowledge provided in the 'interactions_df'. By default, `genes2keep=NULL`, in which case no processing will be applied to the 'interactions_df' object.
<div style="margin-top: 5px;"></div>
-  `genes2remove`: A named list object (same as 'cell_types') of character vectors defining the list of gene-names to keep in the interaction knowledge provided in the 'interactions_df'. By default, `genes2remove=NULL`, in which case no processing will be applied to the 'interactions_df' object.
<div style="margin-top: 5px;"></div>
- `ligands`: A character vector, indicating the names of the ligands that the user wishes to consider for the network inference analysis. By default, `ligands=NULL`, in which case ligands will be considered all the elements defined as such based on the structure of the 'interactions_df' object (see details [above](https://enio23.github.io/lindaplus-docs/documentation/#resources-access)).
<div style="margin-top: 5px;"></div>
- `receptors`: A character vector, indicating the names of the receptors that the user wishes to consider for the network inference analysis. By default, `receptors=NULL`, in which case receptors will be considered all the elements defined as such based on the structure of the 'interactions_df' object (see details [above](https://enio23.github.io/lindaplus-docs/documentation/#resources-access)).

### _runLINDAPlus()_
The main function in LINDA+ that is used to perform the network inference analysis is `runLINDAPlus()` and which can take the following inputs:

* **Mandatory Inputs:**
<div style="margin-top: 5px;"></div>
  - `background.networks.list`: The list consists of two elements: **a)** _background.networks_: This should be a named (cell-types) list containing data-frames joint PPI-DDI's for each cell-type. Each data-frame represents the set of interaction knowledge for each cell-type and it should contain at least 4 columns with the following ID's: 'pfam_source', 'pfam_target', gene_source' and 'gene_target'. In the case where we have no name for a specific domain or where this is not applicable, please set the corresponding values in the 'pfam_source' or 'pfam_target' as NA's (for more details, see in the examples); and **b)** _ligand.receptors_: This also should be a named list ('ligands' and 'receptors') which contains character vectors where the set of elements that corresponds to Ligands and Receptors have been defined as such.
  <div style="margin-top: 5px;"></div>
  - `tf.scores`: This information should be provided as a named list (for each cell-type) and which contains data-frames indicating the enrichment scores for each TF at each cell-type. The data-frames should contain at least two columns: 'tf' (indicating the TF ID) and the numerical 'score' (indicating the enrichment scores for each TF).

<div style="margin-top: 10px;"></div>

* **Optional Inputs:**
  <div style="margin-top: 5px;"></div>
  - `lr.scores`: This information can be provided as a named list (for each cell-type) and which contains data-frames indicating scores that can be assigned to each ligand-receptor interactions targeting a specific cell. The scores should be provided from 0 to 1. The higher the score assigned to a ligand-receptor interaction, the more likely it is for it to be present in the network solution, while in the case of a score of 0, this will force LINDA+ to not include such an interaction in the solution for a specific indicated cell-type.
  <div style="margin-top: 5px;"></div>
  - `ligand.scores`: A data-frame object which should contain two columns: 'ligands' (providing the ligand ID's) and 'score' (providing the score associated to each ligand, i.e. abundance). The scores should be provided either 0 or 1 and the higher the score of the ligand, the more likely it will be for a ligand to appear in the solution. A score of 0, means that this ligand will not be able to perturb any receptor of any cell-type.
  <div style="margin-top: 5px;"></div>
  - `ccc.scores`: A data-frame object which should contain two columns: 'ccc' (providing pairs of interacting cell-types, i.e. "CellA=CellB" for the case when _CellA_ communicates with _CellB_) and 'score' (providing the score associated to each communicating cell pair). The scores should be provided between 0 and 1 and the higher the score a cell-cell interaction, the more likely it will be for the source cell-type to communicate to the target cell-type. A score of 0, means that the defined source cell-type will not talk to the defined target cell-type.
  <div style="margin-top: 5px;"></div>
  - `as.input`: A data-frame object which should contain four columns: 'cell_type' (defining the cell-type in which the splicing effect is happening), 'proteinID' (defining the name of the protein affected by splicing), 'domainID' (defining the PFAM identifier of the protein domain that has been affected by splicing) and 'effect' (defining the type of effect that the alternative splicing has on this specific domain: either _exclusion_ or _inclusion_). In the case of _exclusion_ effect, the corresponding domain will be conidered as skipped and not involved in any of the domain interactions. In the case of _inclusion_, the defined domain will be present in the solution as an interactor.
  <div style="margin-top: 5px;"></div>
  - `solverPath`: Here is provided the location path to the desired solver. By default, the path to the cplex solver is set to: `solverPath = "/usr/bin/cplex"`.
  <div style="margin-top: 5px;"></div>
  - `top.tf`: A named numerical vector defining how many TF's should be considered as significantly enriched for each of the tables provided in the _tf.scores_ input. The names of the elements in the vector should correspond to the desired cell-types. By default, `top.tf = NULL`, in which case all the TF's given in the _tf.scores_ list across each cell-type willbe considered as significantly enriched.
  <div style="margin-top: 5px;"></div>
  - `lambda1`: A numerical value representing the penalization term of the primary objective of the objective function - TF inclusion. This penalty factor is suggested to be set to a higher value compared to other penalty parameters in order to strongly penalize the inclusion of not signifcantly regulated TF's. By default, `lambda1=10`.
  <div style="margin-top: 5px;"></div>
  - `lambda2`: A numerical value representing the penalization term of the ligands nodes in the network solution. It is only used when _ligand.scores_ data-frame object is provided. In this case, by default (when not specified), `lambda2=15`.
  <div style="margin-top: 5px;"></div>
  - `lambda3`: A numerical value representing the penalization term of the ligand-receptor interactions in the network solution. It is only used when _lr.scores_ or _ccc.scores_ or both have been provided. In this case, by default (when not specified), `lambda3=1`.
  <div style="margin-top: 5px;"></div>
  - `lambda4`: A numerical value representing the penalization term of the network components in the solution (proteins and domains). By default, `lambda4=0.1`.
  <div style="margin-top: 5px;"></div>
  - `mipgap`:	CPLEX parameter which sets an absolute tolerance on the gap between the best integer objective and the objective of the best node remaining. When this difference falls below the value of this parameter, the mixed integer optimization is stopped. By default, `mipgap=0`.
  <div style="margin-top: 5px;"></div>
  - `relgap`: CPLEX parameter which sets a relative tolerance on the objective value for the solutions in the solution pool. Solutions that are worse (either greater in the case of a minimization, or less in the case of a maximization) than the incumbent solution by this measure are not kept in the solution pool. For example, if `relgap=0.001` (or 0.1 percent), meaning that then solutions worse than the incumbent by 0.1 percent or more will be discarded. By default, `relgap=0`.
  <div style="margin-top: 5px;"></div>
  - `populate`: CPLEX parameter which sets the maximum number of mixed integer programming (MIP) solutions generated for the solution pool during each call to the populate procedure. Populate stops when it has generated the amount of solutions set in this parameter. By default, `populate=500`.
  <div style="margin-top: 5px;"></div>
  - `nSolutions`: The number of solutions to be provided by LINDA+. By default, `nSolutions=100`.
  <div style="margin-top: 5px;"></div>
  - `timelimit`: CPLEX parameter which sets the maximum optimization time in seconds. By default, `timelimit=3600`.
  <div style="margin-top: 5px;"></div>
  - `intensity`: CPLEX parameter which controls the trade-off between the number of solutions generated for the solution pool and the amount of time or memory consumed. Values from 1 to 4 invoke increasing effort to find larger numbers of solutions. Higher values are more expensive in terms of time and memory but are likely to yield more solutions. By default, `intensity=1`.
  <div style="margin-top: 5px;"></div>
  - `replace`: CPLEX parameter which designates the strategy for replacing a solution in the solution pool when the solution pool has reached its capacity. The value 0 replaces solutions according to a first-in, first-out policy. The value 1 keeps the solutions with the best objective values. The value 2 replaces solutions in order to build a set of diverse solutions. By default, `replace=1`.
  <div style="margin-top: 5px;"></div>
  - `threads`: CPLEX parameter which manage the number of threads that CPLEX uses. By default, `threads=0` (let CPLEX decide). The number of threads that CPLEX uses is no more than the number of CPU cores available on the computer where CPLEX is running.
  <div style="margin-top: 5px;"></div>
  - `condition`: A parameter which can be used in the case when LINDA is desirde to run over multiple analyses in parallel. It is useful to distinguish between the multiple ILP problems defined for each case as well as the solutions obtained. By default, `conditions=1`.
  <div style="margin-top: 5px;"></div>
  - `save_res`: A TRUE/FALSE parameter defining whether you would like to save the results as an _.RData_ object. If `save_res = TRUE`, a 'res_condition.RData' (depending on the value set to _condition_) will be saved in the local working direcotry. By default, `save_res=FALSE`.

## Understaning Output
The output from the _runLINDAPlus()_ analysis consist of a list which contains the following:

- `combined_solutions`: A data-frame which contains information about the inferred interactions in the inter- and the intra-cellular space. There are 4 columns in this data-frame: **i)** _Space_: Showing on which space does a specific interaction belong to (it can either be 'Extra-Cellular' for ligand-receptor interactions or name of the cell-type); **ii)** _Gene Source_: Containing the source protein name of the interaction as a gene ID; **iii)** _Gene Target_: Containing the target protein name of the interaction as a gene ID; **iv)** _Weight_: Indicating how often did an interaction occur among the number of alternative solutions that have been enumerated; and **v)** _DDI_: Telling through which domains are these interactions taking place.
  <div style="margin-top: 5px;"></div>
- `node_attributes`:  A data-frame which contains information about the type of nodes that have been inferred in the network solutions. Every node can take either one of the following attributes: 'Ligand', 'Protein', 'TF', 'Receptor' and 'Domain'. This table can be useful when visualizing the network through special network visualization applications such as [CytoScape](https://cytoscape.org/).
