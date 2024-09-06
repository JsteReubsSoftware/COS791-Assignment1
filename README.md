# COS791-Assignment1
In this assignment we had to build an optimizer capable of optimizing an edge detection pipeline.

## Table of Contents:
- [How to run the program](#how-to-run-the-program)
- [Pipeline Framework Components](#pipeline-framework-components)
- [Our Approach](#our-approach)

<p float="left" align="middle">
  <img src="https://github.com/JsteReubsSoftware/COS791-Assignment1/blob/main/Image_Files/original/Img6.png" width="200" />
  <img src="https://github.com/JsteReubsSoftware/COS791-Assignment1/blob/main/Image_Files/gray_scale/Img6_gray.png" width="200" /> 
  <img src="https://github.com/JsteReubsSoftware/COS791-Assignment1/blob/main/Image_Files/indv_imgs/Img6_indv_pipeline.png" width="200" />
  <img src="https://github.com/JsteReubsSoftware/COS791-Assignment1/blob/main/Image_Files/canny/Img6_Canny.png" width="200" />
</p>

## How to run the program
1. First download all the files or simply clone the repository.
2. Make sure the folder structure is the same as this repository's folder structure.
3. (*)Open the Jupiter Notebook and replace the file path with your file path such that you can load the original image files.
4. Once the images are succesfully loaded, simply run each cell until you reach the section *'Run the program'*.
5. If all cells were successfully executed then run the final cell.
6. The program will then prompt you the following options:
   - '1) General Pipeline' -> Select option 1) if you want to run the GA such that it build a general pipeline based on one image and then applied to all other images.
   - '2) Individual Pipeline' -> Select option 2) if you want the GA to run and build a specific pipeline for each image.
   - '3) Exit the program'
7. Done.
   
**Note: The Jupiter Notebook contains file paths that were used specifically when I worked on this assignment. Make sure to replace them with your respective file paths otherwise the notebook will not run.*

*Note: The program will continue prompting you each time the GA converges and will only terminate when you select option 3.*

*Note: The program also contains file paths to save the output images. You also need to replace these with your chosen file paths.*
   

## Pipeline Framework Components
All images appear to have the same size and has colour. We now need to build a pipeline framework which we can use to apply all the components of edge detection to a single image.

Our proposed framework will consist of the following integral components (steps):

1. Noise Reduction:
- Objective: To diminish the influence of noise, which may obstruct the process of edge detection.
- Options: Gaussian blurring, median filtering, and bilateral filtering.

2. Gradient Calculation:
- Objective: To ascertain the rate of variation in pixel intensity across both horizontal and vertical orientations.
- Options: Sobel, Prewitt, or Laplacian operators are frequently utilized.

3. Edge Thinning (Non-Maximum Suppression):
- Objective: To enhance the detected edges to a singular pixel width.
- Options: By evaluating the gradient magnitude of a pixel in relation to its neighboring pixels, those that do not represent local maxima in the gradient direction are eliminated.

4. Thresholding:
- Objective: To distinguish between genuine edges and noise artifacts.
- Options: Hysteresis thresholding and binary thresholding.

5. Edge Linking:
- Objective: To unify disjointed edge segments.
- Options: Hough transform, gradient direction analysis, and region growing.

## Our Approach
We will make use of a Genetic Algorithm to help find and build an optimal pipeline.

### Chromosome Representation
- Each chromosome will represent a pipeline configuration.
- The genes of each chromosome will comprise of the unique parameters required for the different components of edge detection (see above Pipeline Framework)

### Initial Population Generation
Our algorithm will start by randomly generating unique configurations.
The are the following options for each Gene:
- Gene 1 (Noise reduction):
  - method: 'gaussian', 'median', 'bilateral'
  - params:
      - ksize: (3, 3), (5, 5)
      - sigma: random.uniform(0.5, 2.0)
- Gene 2 (Gradient):
  - method: 'sobel', 'prewitt', 'laplacian'
  - params:
    - ksize: 3, 5
- Gene 3 (Thinning):
  - method: True or False
- Gene 4 (Thresholding):
  - method: 'binary' or 'hysteresis'
  - params:
    - low: random integer in range 30-100
    - high: random integer in range 150-255
- Gene 5 (Linking):
  - method: 'hough', ;region_growing'
  - params:
    - threshold: random integer in range 50-150

*Example Representation*:
```
chromosome = {
            'noise_reduction': {
              'method': 'gaussian',
              'params': {
                'ksize': (3, 3),
                'sigma': random.uniform(0.5, 2.0)
              },
            },
            'gradient': {
              'method': 'laplacian',
              'params': {
                'ksize': 5
              },
            },
            'thinning': True,
            'thresholding': {
              'method': 'hysteresis',
              'params': {
                'low': 35,
                'high': 200
              },
            },
            'linking': {
              'method': 'hough',
              'params': {
                'threshold': 75
              }
            }
        }
```
### Genetic Operators
We will make use of the two common operators: Crossover and Mutation
- Crossover:
  This will involve swapping the genes between two parents to create two offsprings
- Mutation:
  This will involve make some random change to a randomly selected gene

### Selection:
We will use Tournament Selection to select two best parents from a tournament pool.

### Fitness Function:
Our fitness function will simply calculate how well a pipeline performs on an image. We can use metrics like:
- feature descriptor (ORB/SIFT)
- mean-square-error(MSE)
- peak-signal-noise-ratio
- structural-similarity

### GA parameters:
- population size
- generations
- crossover rate
- mutation rate
- tournament size
