# Replication Package

The following are the basic contents of a replication package.

 * `README.md` should describe the contents of the repo, how to use any
   software, how to cite it (you will get a separate DOI for this after
   the process is completed), and how to replicate the experiments in the
   paper. Please make sure to be
   specific about dependencies and anything else that might be important to ensure
   reproducibility (more on this below). Note that the `.md` extension means
   "markdown," a simple text formatting language you can learn about
   [here](https://guides.github.com/features/mastering-markdown/).
   
 * `LICENSE` should be a file containing the text of the license under which
   you intend to distribute the software and/or data. You must provide a
   license in order for the material to be used legally by others. An open
   source license is preferred (see the list of approved licenses at [Open
   Source Initiative](https://opensource.org/licenses), and the license should
   allow free academic use at a minimum. Recommended licenses include the [MIT
   License](https://opensource.org/licenses/MIT) for software or any of the
   various [Creative Commons licenses](https://creativecommons.org/licenses/)
   for other types of material.

 * `AUTHORS` is an optional file, standard in the open source world, that
   lists the authors of the contribution (usually just a list of names and
   e-mails).

 * Depending on how you want to organize things, you may also have a
   `Makefile` or other files needed to build the software and/or run the
   experiments.

 * Subdirectories

   * `src` should contain the source code for any software.

   * `data` should contain data files needed for experiments or used in the
     paper.
     
   * `scripts` should contain any scripts used to replicate the experiments in
     the paper or run other automated tests or experiments.

   * `docs` contains any additional documentation. 

   * `results` should contain any raw results from the paper, as well as
     any plots or figures.

You may wish to have an additional README.md in any of the subdirectories to
provide additional informatio
