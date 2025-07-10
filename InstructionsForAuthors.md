# Procedures for submission of accompanying software and data

[_Note: This page is a supplement to the Code and Data Disclosure Policy
[here](https://pubsonline.informs.org/page/opre/OPRE-code-and-%20data-disclosure-policy)_]

All authors submitting a paper to _Operations Research_ are expected to make the 
data and code necessary to reproduce the results in their paper available 
(unless an exemption has been explicitly asked for and granted).

Submission of software and data can be done in two different ways. The traditional 
process is to submit a tarball along with the paper, but it is also possible to 
create an archive on Github, hosted in the
[ORJournal](https://github.com/ORJournal) Github organization. The latter is
typically the mode of submission when volunteering to participate in a 
replication study. If you have not been contacted about that, you will
most like be following the traditional method of submission.  

If you already host the software and/or data on Gtihub (or elsewhere), you may reference 
that hosting location in the paper, but we still ask authors to either prepare a tarball 
(or an archive on Github) to ensure the exact data and code used to produce the results 
in the paper is preserved along with the paper.  In the below, we provide suggestions 
for how to organize your tarball that are based on the instructions given farther down 
for what to do in order to prepare for a replication study.

## Submitting a Tarball/ZIP archive

To make it easier for reviewers and readers who wish to look at your code and
data to access it, please try to organize the files and directories according
to the following recommendations. Of course, deviations are fine and there are 
no hard rules, just try to organize things in a way that makes it easy to
understand. The first three files described should almost always be there.

 * `README` should describe the contents of the tarball. Ideally, you should
   describe how to use any software and how to replicate the experiments in the
   paper. Please try to be specific about required software dependencies (including
   version numbers), hardware dependencies (e.g., GPUs) and anything else that might
   be important.
   
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

   * `docs` contains any additional documentation. Note that it is possible for
      the contents of `docs` to be a web site that will be hosted under the
      URL https://INFORMSJoC.github.io/NameofRepo. Please let us know if you
      are interested in activating that option.

   * `results` should contain any raw results from the paper, as well as
     any plots or figures.

You may wish to have an additional README in any of the subdirectories to
provide additional information.

## Preparing for a replication study

If you have volunteered for a replication study, then first of all, thank you!
Note that there are two goals for this process. One is to allow editors to
attempt to replicate the experiments and reproduce the empirical data and
results associated with submitted papers. The other is to archive the code in
a way that will be useful to readers and allow the experiments in the paper to
be replicated in the future. For this reason, we would like to include the
full source code needed to perform the experiments in this repository, which
is a fixed snapshot we can guarantee will be available in the future. This is
the case whether or not the code is available elsewhere already (such as in a
Github repo or on Pypi. See the FAQs below for more information.

### Accessing your repository

When requesting your software/data, an editor will give you read access to
to a private repository created by the editorial
staff. This is the repository that should be populated and that will be
reviewed and eventually archived with the paper. The name of the repository
associated with your paper Pypi will be `XXXX.YYYY` and is derived from the
manuscript number in manuscript central. Typically, `XXXX` will be the year in
which the paper is submitted, whereas `YYYY` is a four digit sequence number
that is part of the manuscript number.

### Populating the repository

To populate the repository with your own materials, fork it to
make a copy that will live in your own Github account. Once you populate and
customize the repository to your liking, open a Pull Request to begin the
review.

The contents of the repository should be organized to make it as easy as 
possible for the associate editor to do the replication. The layout should 
resemble that of the template
[here](https://github.com/ORJournal/2024.0000) to the extent possible,
although some variations may occur and there are no inviolable rules. In
general, the repository contents should be as follows.

 * `README.md` should describe the contents of the repo, how to use any
   software, how to cite it (you will get a separate DOI for this after
   the process is completed), and how to replicate the experiments in the
   paper, following roughly the format of the example. Please make sure to be
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

   * `docs` contains any additional documentation. Note that it is possible for
      the contents of `docs` to be a web site that will be hosted under the
      URL https://INFORMSJoC.github.io/NameofRepo. Please let us know if you
      are interested in activating that option.

   * `results` should contain any raw results from the paper, as well as
     any plots or figures.

You may wish to have an additional README.md in any of the subdirectories to
provide additional information.

### Things to keep in mind

When you are submitting your code for a replication study, there are some additional
things to keep in mind. To make it as easy as possible for the editors to replicate
the results in your paper, please provide the following information in your
README.
* Required hardware
  
  * How many CPUs and/or GPUs are needed?

  * How much memory is required?
    
  * What hardware was used in the experiments reported in the paper?
    
* Required software

  * Programming of modeling language
    
  * Additional dependencies (especially if proprietary)
    
  * Operating system

* Required data (if not included in the repository)
    
Please also provide
* Detailed instructions for installation of software
  
* Scripts for running the experiments, compiling the data, and producing the tables and figures

* Output logs from experiments done by the authors (if reproducing all experiments would be too
  time-consuming).

In case replicating all experiments would be a long process, please divide the experiments into
subsets that can be run in a reasonable amount of time and can verify an easily identifiable part
of the reported results.

### Review process 

The review process begins as soon as you submit your pull request. As part of the review process, 
additional changes may be requested. If so, the area editor will communicate with you by putting
comments into the pull request. You can make the requested changes by updating your fork and these 
will be immediately reflected in the pull request. Once everything is finalized, then the pull request
will be accepted. Upon publication of the paper, the repository will be made public. 

### Legal stuff

Please ensure that all files contain proper copyright and licensing statements
and that the copyright holders have been notified of the submission. The
copyright holder may or may not be you, depending on your employment contract
and who funded the work.

### Archive and DOI

Once accepted by the editor, a snapshot of the contents of the repo will be archived 
by creating a tag (known as a release on Github) with the name `vXXXX.YYYY`, where
the repo's name is `XXXX.YYYY`. This paper and the snapshot of the repo will be given 
their own separate DOIs, also derived from the manuscript number. If the repo is named
`XXXX.YYYY`, then the DOIs will be

https://doi.org/10.1287/opre.XXXX.YYYY

https://doi.org/10.1287/opre.XXXX.YYYY.cd

### Citing the repo in your paper

The repo must be cited in your paper, as a regular reference, and appear in the list of references as follows (if using BibTex). Notice that you must use both the DOI and the Note lines to make this reference appear correctly.
```
@misc{CacheTest,
  author =        {Put the authors' names here},
  publisher =     {Operations Research},
  title =         {Put the title of your paper here},
  year =          {put the year here - formal like 2023},
  doi =           {Put your DOI here - see above - example: 10.1287/opre.2019.0000.cd},
  note =          {Again, use your DOI here at the end of the link.  example: Available for download at https://github.com/ORJournal/2024.0000},
}  
```

### FAQs

 * In the process of the review, changes were made to the software and/or
 data that was originally submitted and I don't want the original version of
 the software or data to be made public. Can you delete the history associated
 with the repository before making it public?

   * We can replace the repository used for the review process with a clean
     copy if you do not want the history made public.

 * What if I have an existing repository where the software is already being
   developed? Can I continue to develop there?

   * We expect this to sometimes be the case. But we still need to archive
     the version of the software and/or data associated with the
     paper itself in a static and permanent repository within the OR Github organization.
     If you wish us to Fork or move an existing repository into the OR
     organization as part of the submission process, that can be discussed.

 * What if I don't already have an existing repository, but I want to continue
   developing the software after the paper is published?

   * This is highly encouraged, but further development should take place on
     a personally managed site on Github or one of the other similar sites.
     You should put a link to the site where you will manage the software in
     the long-term in the README.md in the OR repository to ensure people
     can find your development site.  You cannot continue development on the OR Github site.

 * What if I later find a bug in the software and I want to fix it?

   * Please contact the Area Editor and we will determine the best course of
     action.

 * If I come out with a new version of the software later on, can I add it to
   the existing repository within the OR organization?

   * At the current time this is not done.  The answer to this may evolve over time. Please contact us if/when this
     happens and we will make a determination.
