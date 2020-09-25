## Accessory Tools

Several accessory tools are provided with PlantCV. These tools are installed in your system or environment path or are
available by downloading/cloning the GitHub repository.

### Training machine learning models

`plantcv-train.py` is a command-line tool for training machine learning classifiers or other models in PlantCV. More
detail is provided in the [Machine Learning Tutorial](machine_learning_tutorial.md) but command/input details are
provided below:

```
usage: plantcv-train.py [-h] {naive_bayes,naive_bayes_multiclass}

Subcommands:
    naive_bayes
        usage: plantcv-train.py naive_bayes [-h] -i IMGDIR -b MASKDIR -o OUTFILE [-p]
        
        optional arguments:
            -h, --help                       Show this help message and exit
            -i IMGDIR, --imgdir IMGDIR       Input directory containing images.
            -b MASKDIR, --maskdir MASKDIR    Input directory containing black/white masks.
            -o OUTFILE, --outfile OUTFILE    Trained classifier output filename.
            -p, --plots                      Make output plots.
        
    naive_bayes_multiclass
        usage: plantcv-train.py naive_bayes_multiclass [-h] -f FILE -o OUTFILE [-p]
        
        optional arguments:
            -h, --help                       Show this help message and exit
            -f FILE, --file FILE             Input file containing a table of pixel RGB values sampled for each input 
                                             class.
            -o OUTFILE, --outfile OUTFILE    Trained classifier output filename.
            -p, --plots                      Make output plots.

```

### PlantCV utilities

`plantcv-utils.py` is a command-line tool for running various utilities.

**Source Code:** [Here](https://github.com/danforthcenter/plantcv/blob/master/plantcv-utils.py)


#### Convert output JSON data files to CSV tables

`plantcv-utils.py json2csv` is a command-line tool for converting the output JSON files from `plantcv-workflow.py` to
CSV-formatted tables for downstream analysis in [R](https://www.r-project.org/), 
[MVApp](http://mvapp.kaust.edu.sa/MVApp/), or other programs.

```
usage: plantcv-utils.py json2csv [-h] -j JSON -c CSV

optional arguments:
  -h, --help            Show this help message and exit
  -j JSON, --json JSON  Input PlantCV JSON filename.
  -c CSV, --csv CSV     Output CSV filename prefix.

```

The input JSON file is generally created by running `plantcv-workflow.py`, although there are some advanced scenarios
where it is created by running `plantcv.parallel.process_results` on a directory of JSON files. This 
[hierarchical data structure](output_measurements.md) is convenient for flexible data processing but not for downstream
analysis. The tool creates two output CSV files named with the user-provided prefix. `prefix-single-value-traits.csv`
is a table of observations with single values (e.g. area, convex hull area, etc.). The format of this table is one row
per image. `prefix-multi-value-traits.csv` is a table of observations with multiple values (e.g. frequency distribution
of hue or other color channel/properties). The format of this table is one row per value/label. 

**Source Code:** [Here](https://github.com/danforthcenter/plantcv/blob/master/plantcv/utils/converters.py)


#### Collect a random sample of images for testing workflows

`plantcv-utils.py sample_images` is a command-line tool for gathering a random set of images for
testing workflows before running over a full image set. 

```
usage: plantcv-utils.py sample_images [-h] -s SOURCE_DIRECTORY -o OUTPUT_DIRECTORY -n NUMBER

optional arguments:
  -h, --help                                      Show this help message and exit
  -s SOURCE_DIRECTORY, --source SOURCE_DIRECTORY  Input image directory.
  -o OUTPUT_DIRECTORY, --outdir OUTPUT_DIRECTORY  Output directory.
  -n NUMBER, --number NUMBER                      Number of images to randomly select. (default=100)

```

Testing a workflow on small test set (that ideally spans time and/or treatments) can speed up workflow optimization and 
test it on other images in the dataset to determine how robust the workflow will be. The random image sampler can help 
identify 'problem images' before running a workflow in parallel over a large set of images. This
tool can handle LemnaTec structured output in addition to a flat file directory. Currently supported image types include 
.png, .jpg, .jpeg, .tif, .tiff, and .gif. An output directory will be created if it does not already exist. The number of 
random images requested must be less than or equal to the number of images in the source directory. 

**Source Code:** [Here](https://github.com/danforthcenter/plantcv/blob/master/plantcv/utils/sample_images.py)


### Parallel workflow processing

`plantcv-workflow.py` is a command-line tool for parallel processing of user-defined PlantCV workflows. It is used to
process metadata and execute custom workflows on each image in a dataset. More detail is provided in the 
[Workflow Parallelization Tutorial](pipeline_parallel.md) but command/input details are provided below:

```
usage: plantcv-workflow.py [-h] -d DIR [-a ADAPTOR] -p WORKFLOW -j JSON
                           [-i OUTDIR] [-T CPU] [-c] [-D DATES] [-t TYPE]
                           [-l DELIMITER] [-f META] [-M MATCH] [-C COPROCESS]
                           [-w] [-o OTHER_ARGS]

Parallel imaging processing with PlantCV.

optional arguments:
  -h, --help            
                        Show this help message and exit
  -d DIR, --dir DIR     
                        Input directory containing images or snapshots.
                        (default: None)
  -a ADAPTOR, --adaptor ADAPTOR
                        Image metadata reader adaptor. PhenoFront metadata is
                        stored in a CSV file and the image file name. For the
                        filename option, all metadata is stored in the image
                        file name. Current adaptors: phenofront, filename
                        (default: phenofront)
  -p WORKFLOW, --workflow WORKFLOW
                        Workflow script file. (default: None)
  -j JSON, --json JSON  
                        Output database file name. (default: None)
  -f META, --meta META  
                        Image file name metadata format. List valid metadata
                        fields separated by commas.
                        Valid metadata fields are: camera, imgtype, zoom,
                        exposure, gain, frame, lifter, timestamp, id,
                        plantbarcode, treatment, cartag, measurementlabel,
                        other (default: imgtype_camera_frame_zoom_id)
  -i OUTDIR, --outdir OUTDIR
                        Output directory for images. Not required by all
                        workflows. (default: .)
  -T CPU, --cpu CPU     
                        Number of CPU to use. (default: 1)
  -c, --create          
                        Will overwrite an existing database. Warning: activating
                        this option will delete an existing database!
                        (default: False)
  -D DATES, --dates DATES
                        Date range. Format: YYYY-MM-DD-hh-mm-ss_YYYY-MM-DD-hh-
                        mm-ss. If the second date is excluded then the current
                        date is assumed. (default: None)
  -t TYPE, --type TYPE  
                        Image format type (extension). (default: png)
  -l DELIMITER, --delimiter DELIMITER
                        Image file name metadata delimiter character. Alternatively, a regular expression for parsing filename metadata.
                        (default: _)
  -M MATCH, --match MATCH
                        Restrict analysis to images with metadata matching
                        input criteria. Input a metadata:value comma-separated
                        list. This is an exact match search. E.g.
                        imgtype:VIS,camera:SV,zoom:z500 (default: None)
  -C COPROCESS, --coprocess COPROCESS
                        Coprocess the specified imgtype with the imgtype
                        specified in --match (e.g. coprocess NIR images with
                        VIS). (default: None)
  -s timestampformat, --timestampformat timestampformat
                        A date format code compatible with strptime C library.
                        e.g. "%%Y-%%m-%%d %%H_%%M_%%S", except "%%" symbols must be escaped on Windows with "%%" e.g. "%%%%Y-%%%%m-%%%%d %%%%H_%%%%M_%%%%S". 
                        (default: "%%Y-%%m-%%d %%H:%%M:%%S.%%f")
  -w, --writeimg        
                        Include analysis images in output. (default: False)
  -o OTHER_ARGS, --other_args OTHER_ARGS
                        Other arguments to pass to the workflow script.
                        (default: None)

```

**Source Code:** [Here](https://github.com/danforthcenter/plantcv/blob/master/plantcv-workflow.py)

