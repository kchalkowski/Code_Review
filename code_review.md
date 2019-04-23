# Code Review (BIOL 7180)
## for A large-scale analysis of bioinformatics code on GitHub, Russell et al. 2018

The ability to reproduce experiments is foundational to the scientific process. Likewise, when the experiment in question consists of a code used to run analyses, reproducibility is desired such that other researchers can understand and reproduce the resulting data. In keeping in line with this objective, I will conduct a code review of a perl pipeline created to analyze patterns in bioinformatics code on github “between code properties, development activity, developer communities, and software impact” (Russell et al. 2018). I chose this code in part because, in contrast to many source codes available for R packages I had looked through, for example, it is well commented and partitioned in such a way that even without a good understanding of the Perl scripting language, I am able to follow along with the code and understand its components. 

The code being reviewed below can be found at: https://github.com/pamelarussell/github-bioinformatics/blob/master/src/pipeline/github_bioinformatics_pipeline.pl

### Header
Libraries/modules in the header are clearly defined and separated at the top of the script. Following this, all keys/cariable are clearly laid out 
```perl

#!/usr/bin/perl

use strict;
use warnings;
use File::Slurp;
use JSON;


# -----------------------------------------------------------------
#                    Pipeline configuration
# -----------------------------------------------------------------

# Key names for config file
my $key_gh_user = "gh_username";
my $key_gh_oauth_key = "gh_oauth_key";
my $key_json_key_google = "json_key";
my $key_bq_proj = "bq_proj";
my $key_gsheet_repos = "gsheet_repos";
.....
```

### Defined keys after help message
All variables previously defined above are clearly labeled, for example, $key_gh_user is a variable for the Github username. Definitions of keys are also presented in the same order as the actual variable list above, making it easy for others to refer back
```perl
"Usage: github_bioinformatics_pipeline.pl <config.json>\n" .
				"\n" .
				"Config file is JSON with top-level keys:\n" .
				"\n" .
				"* GitHub API configuration *\n" .
				"\n" .
				"'$key_gh_user': GitHub username\n" .
				"'$key_gh_oauth_key': GitHub Oauth key\n" .
				"\n" .
				"* Google configuration *\n" .
				"\n" .
				"'$key_json_key_google': File with JSON key for Google credentials\n" .
				"'$key_bq_proj': Name of BigQuery project\n" .
				"'$key_gsheet_repos': Name of Google Sheet with curated article and repo info\n" .
				"'$key_gcs_bucket': Name of Google Cloud Storage bucket for project data\n" .
				"\n" .

```perl
