# ESO MUSE Hyperspectral Data Cube Pipeline

A Python pipeline to collect hyperspectral data cubes from the ESO archive for astronomical images and fields.

This program takes an astronomical input image, solves its astrometric coordinates via Astrometry.net, performs a parity and North-up rotation alignment, queries the ESO TAP service using a footprint polygon intersection, and downloads overlapping VLT/MUSE Level-3 hyperspectral data cubes. It also returns a rotated input image aligned with standard coordinates for visual verification.

Please note that your input image must be an object that falls within the ESO MUSE observation range, which primarily includes Southern Hemisphere objects

Also note that this program has not yet been updated to support cube stitching. So, it will not compile a full hyperspectral image, but it allows you to downlaod all the pieces and makes stitching possible. 

There is a wavelength gap in ESO-MUSE data from 582 nm -597 nm

---

## Technical Specifications

* **Instrument:** VLT / MUSE (Multi-Unit Spectroscopic Explorer at ESO Cerro Paranal)
* **Data Product Level:** Level-3 (Science-Ready, Fully Reduced Datacubes)
* **Wavelength Range:** 480 nm – 930 nm (nominal mode / nominal range across (~3,681 spectral slices/bands, varying by data reduction pipeline version)
* **Spatial Resolution:** Wide-Field Mode (WFM) sampled at $0.2^{\prime\prime} / \text{pixel}$ over a $1^\prime \times 1^\prime$ field of view
* **Spectral Dispersion:** 0.125 nm per pixel
* **Input File Formats:** FITS (`.fits`), TIFF (`.tif`, `.tiff`)

## Prerequisites & Installation

1. **Python Dependencies:**
   Ensure you have the required scientific libraries installed:
   ```bash
   pip install astropy astroquery pyvo requests pillow numpy scipy tifffile imagecodecs
   ```
2. **Astrometry.net API Key:**
   Obtain a free API key from Astrometry.net.
   Set it as an environment variable on your system:
   ```bash
   export ASTROMETRY_API_KEY="your_key_here"
   ```
3. **Usage**
   Option 1: Jupyter Notebook (Recommended)
   Running this program inside a Jupyter Notebook is recommended for easy configuration, as there are several variables to adjust depending on your input frames.

   Edit the user defaults directly in the configuration section of the script under "User Inputs":

   ```python
    BASE_DIR = Path("./SOLVE") # Base working directory
    INPUT_DIR = BASE_DIR / "INPUT" # Input directory; Where are your files stored (.tif, .tiff, .fits files required)

    ASTROMETRY_API_KEY = os.getenv("ASTROMETRY_API_KEY", "your_actual_astrometry_key_here")

    ESO_USERNAME = os.getenv("ESO_USERNAME", None) # Optional: your ESO portal username
    ESO_PASSWORD = os.getenv("ESO_PASSWORD", None) # Optional: your ESO portal password

    # NOTE: ESO MUSE Level-3 cubes are monolithic full-resolution files (spanning 465 nm to 930 nm across ~3,681 bands).
    # The download protocol retrieves whole data cubes directly from the ESO data portal.
    MAX_CUBES = 1  # Limit the number of cubes downloaded per field for testing. Set to None to download ALL.
    ```

   Option 2: Command Line Interface (CLI)
   If you prefer running the script from the terminal, you can pass arguments on the fly:
   
   ```bash
   python pipeline.py --base-dir "./SOLVE" --max-cubes 2 --api-key "your_key_here"
   ```


   Available CLI Arguments:

    --base-dir: Base working directory for pipeline outputs (default: ./SOLVE)

    --input-dir: Input directory containing source images (default: <base-dir>/INPUT)

    --api-key: Astrometry.net API key (overrides environment variable if provided)

    --eso-user: ESO User Portal username for proprietary data access

    --eso-pass: ESO User Portal password for proprietary data access

    --max-cubes: Limit the number of matching cubes downloaded per field (default: None)
   