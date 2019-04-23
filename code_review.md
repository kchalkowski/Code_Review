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
All variables previously defined above are clearly labeled, for example, $key_gh_user is a variable for the Github username. Definitions of keys are also presented in the same order as the actual variable list above, making it easy for others to refer back. Additionally, actual variable names are fairly straightforward and indicative of what the purpose of the variable is.
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

```

#Names for arguments
Argument names are clearly defined, for example, a produced error message "Missing argument: config file" is aptly named "die_with_message" which helps users understand the purpose of this line of code. Additionally, 
```perl
my $json_config = $ARGV[0] or die_with_message("Missing argument: config file");
```

#Project Structure
again, project structure is well organized with a header, and subheaders defining the different types of datasets. Also, analysis tables are well commented and it is very clear what each table describes. However, it would be nice to have a little more commenting and description of what each dataset is. For example, is $bq_tb_commits the number of commits per user? or per project? 

```perl

# -----------------------------------------------------------------
#                   BigQuery project structure
# -----------------------------------------------------------------

# Datasets
my $bq_ds_repos = "repos";
my $bq_ds_analysis_results = "analysis";
my $bq_ds_lit_search = "lit_search";
# Data tables with data pulled from GitHub API
my $bq_tb_repo_metrics = "repo_metrics";
my $bq_tb_commits = "commits";
my $bq_tb_contents = "contents";
my $bq_tb_files = "file_info";
my $bq_tb_file_init_commit = "file_init_commit";
my $bq_tb_languages_gh_api = "languages_gh_api";
my $bq_tb_licenses = "licenses";
my $bq_tb_prs = "pull_requests";
# Analysis tables
my $bq_tb_lang_bytes_by_repo = "language_bytes_by_repo"; # Total bytes of code per language per repo
my $bq_tb_bytes_by_language = "bytes_by_language"; # Total bytes of code per language across all repos
my $bq_tb_lang_list_by_repo = "language_list_by_repo"; # List of languages used by repo
my $bq_tb_num_langs_by_repo = "num_langs_by_repo"; # Number of languages used by repo
my $bq_tb_num_repos_by_lang = "num_repos_by_lang"; # Number of repos using each language
(cont'd)....

```
### Workflow
Again, the header of the new section is well defined, and then the steps are laid out with a comment describing each series of commands.  Additionally, the if/else print statements further clarify what the code is doing. In the first block of code below, for example, the else statement is to print a string that read "Skipping step, extract repo names from literature search"." The only thing that would make this a little clearer is an overall preceding description of the chronology of how the code runs and how everything fits together.

```perl
# -----------------------------------------------------------------
#                     Run parts of the workflow
# -----------------------------------------------------------------

# Extract GitHub repo names from literature search - includes non-bioinformatics repos
if($extract_repos_from_lit_search) {
	run_cmmd("$python3 $script_extract_repos_from_lit_search --metadata-dir $lit_search_metadata_dir ".
	"--pdf-dir $lit_search_pdf_dir --bq-ds $bq_ds_lit_search --bq-tb $bq_tb_repo_article")
} else {print("\nSkipping step: extract repo names from literature search\n")}
...(cont'd)
```
###Subroutines
This subroutine code is helpful in keeping directories clean, especially if a command is run multiple times, by deleting previous versions of files. It is also offered as an option rather than embedded into the program, which is ideal because there may be instances where previous output needs to be retained.

```perl 
# -----------------------------------------------------------------
#                          Subroutines
# -----------------------------------------------------------------

# Run a command and delete a previous version of the output if it exists
# Args:
#   1. The command
#   2. (Optional parameter) The output file to delete before running the command 
sub run_cmmd {
	my ($cmmd, $output) = @_;
	$output //= 0;
	print("\nRunning command: $cmmd\n");
	# Delete the previous version of the output file if it exists
	if($output && -e $output) {
		system("rm", $output);
		if( $? != 0 ) {die "Could not remove previous output file $output\n";}
		print("Deleted previous output file $output\n");
	}
	system($cmmd);
	if ( $? != 0 ) {die;}
}
```

###Conclusion
In conclusion, this is an example of a well-structured, reproducible script. Each section of the code (pipeline configuration, project structure, etc) are clearly defined, and variables are described precisely. The working section of the code is also well commented, with a header comment describing what each loop does. The only thing I would recommend in this code to the creators would be to describe how each part of the code fits together. For me, it is still a little unclear how each piece in the workflow results in one final resulting data-set. 
