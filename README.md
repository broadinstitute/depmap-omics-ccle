# depmap-omics-ccle

DepMap currently stores raw omics data from publicly-releasible cell line models generated from the [Cancer Cell Line Encyclopedia (CCLE)](https://sites.broadinstitute.org/ccle) on the [Registry of Open Data on AWS](https://registry.opendata.aws/). See [our page](https://registry.opendata.aws/depmap-omics-ccle) on the registry for a description of the data set and also the data dictionary `depmap-omics-ccle-datadict.json` in this repo. Currently included are WES/WGS/RNA CRAM/BAM files for ~1000 cancer cell lines.

## Analyzing the data

### AWS HealthOmics

[AWS HealthOmics](https://docs.aws.amazon.com/omics/) can be used to analyze the raw data (i.e. CRAM/BAM files) on AWS without manually provisioning all the underlying infrastructure. This includes [Ready2Run workflows](https://docs.aws.amazon.com/omics/latest/dev/workflows-r2r-table.html) like [NVIDIA Parabricks](https://docs.nvidia.com/clara/parabricks/latest/index.html) for WGS preprocessing, somatic variant calling, RNA alignment, and more.

#### DepMap workflows

Workflows used by [the DepMap portal](https://depmap.org/) are actively maintained in the case of WGS and short read RNA. These are stored on GitHub:

- [WGS workflows](https://github.com/broadinstitute/depmap-omics-wgs/tree/main/workflows)
- [RNA workflows](https://github.com/broadinstitute/depmap-omics-rna/tree/main/workflows)

Each workflow is written in WDL and is accompanied by a JSON file with default workflow input values, e.g. `workflows/align_sr_rna/align_sr_rna.wdl` and `workflows/align_sr_rna/align_sr_rna_inputs.json`, which can be used as HealthOmics [parameter template files](https://docs.aws.amazon.com/omics/latest/dev/parameter-templates.html).

##### Container images

Some tasks use public Docker images on DockerHub, but most are custom images built by us. Their Dockerfiles can be found either alongside the WDL (e.g. `workflows/quantify_sr_rna/salmon.Dockerfile`) or in the `workflows/common` folder if the image is shared by multiple workflows (e.g. `workflows/common/star_arriba.Dockerfile`). 

For latency reasons (and because our container registry is private and on Google Cloud) we recommend building your own images from these Dockerfiles and pushing them to [Elastic Container Registry](https://docs.aws.amazon.com/ecr) in the AWS region where you run HealthOmics workflows. Then, the `docker_image` and `docker_image_hash_or_tag` task inputs can be overridden to refer to your image URLs in ECR.

See the [HealthOmics container images docs](https://docs.aws.amazon.com/omics/latest/dev/workflows-ecr.html) for more information on where your workflow's images can be stored.

#### Other workflows

We also recommend the [GATK Best Practices](https://gatk.broadinstitute.org/hc/en-us/sections/360007226651-Best-Practices-Workflows) site as a reputable source of WDL workflows.

In addtion to WDL, you can run your own private HealthOmics workflows written in CWL or Nextflow.  

### Nextflow

Running Nextflow workflows is also supported in AWS with AWS Batch as the execution backend. See the [Nextflow AWS docs](https://www.nextflow.io/docs/edge/aws.html) for instructions or read [Seqera's tutorial](https://seqera.io/blog/nextflow-and-aws-batch-inside-the-integration-part-1-of-3/). Verified Nextflow workflows for analyzing omics data can be found at [nf-core](https://nf-co.re/).
