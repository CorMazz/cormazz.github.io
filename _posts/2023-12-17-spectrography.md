---
layout: post
title: Fourier Time Series Analysis In Python
subtitle: Data Segmentation with the STFT and DBSCAN Clustering
gh-repo: cormazz/spectrography
gh-badge: [star, fork, follow]
thumbnail-img: /assets/blog_posts/bp.spectrography/cover_image.png
share-img: /assets/blog_posts/bp.spectrography/cover_image.png
tags: [machine-learning, python, clustering, dbscan, spectrogram, stft, ]
comments: true
author: Corrado R. Mazzarelli
---

{: .box-success}
This article explores separating parts of a signal based on the dominant frequency in that part of the signal. All the material used to create this is within the [GitHub repository](https://github.com/cormazz/spectrography). **I strongly encourage you to explore the [resources](#resources) linked below.** They have guided me on my engineering journey and it is truly some remarkable information, all available for free. This article will be written with the assumption that you are familiar with Python, and have watched the reference videos on the discrete Fourier transform, the fast Fourier transform, the Uncertainty Principle, and clustering algorithms. 

* Do not remove this line (it will not be displayed)
{:toc}

# Summary
While working at GE, I did my best to seek out fun data science projects to sate intellectual curiosity. Two years ago, someone approached me and asked if there was a way to cluster periodic data based on its frequency. The goal was to take a plot of data that looked like this:

### Figure 1: Initial Data
[Standalone Figure](https://corradomazzarelli.com/assets/blog_posts/bp.spectrography/initial_data.html)
{% include bp.spectrography/initial_data.html %}

and label the different sections based on their frequencies. 

I did this by transforming the data into the frequency domain where high and low frequencies could easily be seen using the short-time-Fourier-transform, which essentially takes the normal Fourier transform but on a rolling window, thus trading temporal certainty for spectral certainty. That spectrogram looked like this:

### Figure 2: Spectrogram
[Standalone Figure](https://corradomazzarelli.com/assets/blog_posts/bp.spectrography/spectrogram.html)
{% include bp.spectrography/spectrogram.html %}

This plot shows the energy in each frequency at a given time. From here, the dominant frequencies were drawn out by simply finding the frequency with the most energy (the most yellow on the plot) at a certain time, and plotted. 

### Figure 3: Dominant Frequency Plot
[Standalone Figure](https://corradomazzarelli.com/assets/blog_posts/bp.spectrography/dominant_frequency_plot.html)
{% include bp.spectrography/dominant_frequency_plot.html %}

It would be simple to draw a line to separate out the high frequency from the low frequency data; however, that would neglect the temporal separation of the different sections of data. Thus, DBSCAN clustering was used to temporally and spectrally cluster the data.

### Figure 4: Clustered Dominant Frequency Plot
[Standalone Figure](https://corradomazzarelli.com/assets/blog_posts/bp.spectrography/clustered_dominant_frequencies.html)
{% include bp.spectrography/clustered_dominant_frequencies.html %}

Once the hyperparameters were tuned, the DBSCAN algorithm did an excellent job segmenting the data into different clusters. The identified clusters were then mapped onto the original data, and the final plot was created.

### Figure 5: Final Clustered Data
[Standalone Figure](https://corradomazzarelli.com/assets/blog_posts/bp.spectrography/clustered_data.html)
{% include bp.spectrography/clustered_data.html %}

{: .box-note}
**Note:** Try moving the plot around, zooming in, and clicking on legend entries. If you like these plots, look into [Plotly](https://plotly.com/python/) which allows you to save interactive plots as html files.

In conclusion, this project demonstrated the synergy between signal processing techniques like STFT, frequency analysis, and clustering algorithms like DBSCAN. By combining these methods, I successfully transformed and clustered periodic data, providing a comprehensive and insightful representation of the underlying patterns in the original time series.

# Introduction

## Context
I'm not going to go too in depth here regarding what testing GE was doing. However, the data was from a wear test rig where different samples of material were rubbed against each other and properties such as load and displacement were recorded. This data could be used to help describe material properties to let engineers predict the life of different gas turbine components. In this case, there were low-frequency, high-amplitude sections of the data interspersed between high-frequency, low-amplitude sections. 

## Personal History and Motivation
When I first wrote the script for GE 2 years ago, I had never heard of a spectrogram and had just learned about the Fourier transform and its uses. Funnily enough, when I was trying to use the Fourier transform to determine the frequency of the signal at a certain time, I had the idea to take a "rolling Fourier transform," which is exactly what the spectrogram is. I "independently discovered" the spectrogram and made a crude interpretation of it in Python. After following some Dr. Brunton's lecture series (in the [resources](#resources)) and I learned about the spectrogram, I decided to endeavor to rewrite my script for fun, implementing my newfound tools to improve it and then give the improved version to my friend over in the Wear Laboratory. 

## Interesting Concepts

### Short-Time Fourier Transform
The Short-Time Fourier Transform is a signal processing technique used to analyze the frequency content of a signal over time. Unlike the traditional Fourier Transform, which provides information about the entire duration of a signal, STFT breaks the signal into short, overlapping time segments and performs a Fourier Transform on each segment. This allows for a more detailed examination of how the frequency components of a signal change over time, making STFT particularly useful in applications such as audio signal processing and time-frequency analysis.

### Uncertainty Principle
The Uncertainty Principle is a fundamental concept in quantum mechanics, formulated by Werner Heisenberg. It states that certain pairs of physical properties (such as position and momentum) cannot both be precisely measured simultaneously. The more accurately one property is known, the less accurately the other can be known. This principle challenges the classical notion of absolute precision in measurements and introduces a fundamental limit to the predictability of particle behavior at the quantum level.

When considering the Uncertainty Principle in the context of the Short-Time Fourier Transform (STFT), it's helpful to explore how this principle manifests as temporal and spectral uncertainty in the analysis of signals.

**Short Time Windows:** Using shorter time windows in the STFT improves temporal resolution by capturing rapid changes in the signal. However, this comes at the cost of frequency resolution. Short windows are less effective in accurately identifying the frequency content of signals with longer durations or those that change more slowly over time.

**Long Time Windows:** Conversely, using longer time windows enhances frequency resolution but diminishes temporal resolution. Longer windows are better suited for capturing the frequency components of slowly varying signals but might miss the nuances of rapidly changing parts of the signal (ie, exactly when the signal switches from low frequency to high frequency)

#### Effects
The Uncertainty Principle manifests itself in two places. First, if you zoom in on the [final identified clusters plot](#figure-5-final-clustered-data) at a transition between low and high frequency, you will notice a small portion of the signal misidentified as noise. This is because there is not enough temporal accuracy to determine when exactly the signal switches between frequencies. Second, if you look at the legend that plot, the frequencies of the different clusters are identified as 1.17 Hz and 30.51 Hz. The actual frequencies are 0.05 Hz and 30 Hz. This is an artifact of the spectral uncertainty that we incurred when we gained temporal certainty. 

In summary, the Uncertainty Principle introduces a fundamental constraint on the joint precision of time and frequency measurements in signal analysis. This principle influences the design choices made when configuring the parameters of the STFT, such as the choice of window size and overlap. Striking a balance between time and frequency resolutions is crucial to effectively extract meaningful information from signals while acknowledging the inherent limitations imposed by the Uncertainty Principle.

### DBSCAN Clustering
DBSCAN (Density-Based Spatial Clustering of Applications with Noise) is a clustering algorithm commonly used in machine learning and data analysis. Unlike traditional clustering algorithms that assume clusters have a specific shape, size, or density, DBSCAN identifies clusters based on the density of data points in a region. It can find clusters of arbitrary shapes and is robust to noise. DBSCAN categorizes points as core points, border points, or noise, providing a flexible and effective approach to cluster analysis.

### Polars Lazy Execution
Polars is a data manipulation library in the Rust programming language that introduces the concept of lazy execution. Lazy execution delays the actual computation until the result is explicitly needed, optimizing performance by avoiding unnecessary calculations. Polars allows users to build a sequence of transformations on large datasets without immediately computing the results. This can be advantageous when working with big data, as it enables more efficient resource utilization and can lead to faster overall processing times when the final results are requested.

### Aliasing
Aliasing is a phenomenon in signal processing and digital signal analysis where a signal's true characteristics become distorted or misrepresented due to undersampling or inadequate sampling rates. This occurs when a signal contains frequencies higher than half of the sampling rate (the Nyquist frequency), leading to the misinterpretation of these high-frequency components as lower frequencies.

In the context of digital signal processing, aliasing can result in the creation of false, lower-frequency components that were not present in the original signal. This phenomenon is commonly observed when a signal is improperly sampled or when its frequency content exceeds the Nyquist limit, causing the sampled signal to "fold" back into lower frequencies.

Aliasing can introduce errors and artifacts in various applications, including audio processing, image processing, and data acquisition. To mitigate aliasing, it is essential to adhere to proper sampling practices, ensuring that the sampling frequency is sufficiently high to accurately represent the signal's frequency content without introducing distortions. Anti-aliasing filters are often employed to remove high-frequency components before sampling, preventing aliasing and preserving the fidelity of the original signal during digitization. 

In this case, zoom in on the high frequency portion of [the data](#figure-1-initial-data). You'll notice until you get really, really close, the data does not look like a proper sine wave. This is because when you are zoomed out, you're witnessing the effects of aliasing and the data appears to be a different frequency than it really is. 

# Python Libraries

## Numpy
[Numpy](https://numpy.org/) is a powerhouse in scientific Python analysis, and needs no introduction.

## Polars
Instead of using Pandas, I opted for [Polars](https://pola.rs/). Polars is essentially much faster Pandas that has a lazy API which was useful for processing the ~billion data points in the data from the lab. One downside to Polars is that it is still new and does not have the community support/integration that Pandas enjoys. 

## Scipy
[Scipy](https://scipy.org/) is another powerhouse in scientific Python analysis. Here specifically I leveraged scipy.signal.spectrogram, which automatically calculates the spectrogram for you.

## Scikit-Learn
Beloved to machine learning enthusiasts everywhere, [scikit-learn](https://scikit-learn.org/stable/index.html) contains prebuilt machine learning models ready to implement. In this case, I leveraged their data scaling and DBSCAN clustering implementations. 

## Plotly
[Plotly](https://plotly.com/python/) is a widely used plotting library which enables easy interactive plots which can be saved as interactive. They are the plots you'll see embedded here.

## Plotly-Resampler
[Plotly-resampler](https://github.com/predict-idlab/plotly-resampler) is a Python library that utilizes Dash to create a dynamically updating Plotly chart that only displays a limited number of data points to ensure the plot doesn't become bloated and slow. This was very useful for the GE implementation, since there were about a billion data points to plot. A limitation of plotly-resampler is that it requires an active Python kernel, so the plots cannot be saved as interactive.

## Tqdm
[Tqdm](https://tqdm.github.io/) is just a progress bar library. I like to use it to let me know how long my scripts are taking. 

{: .box-note}
**Note:** An environment.yml file is included in the GitHub repository to allow you to recreate a functional Anaconda environment.

# Code Implementation

## Generate Data
First we have to generate some representative data. I did this using numpy to generate a few different sinusoids with the desired frequencies and amplitudes and then save that data as a .csv. The original data is the first plot shown above. If you want more data, check out `generate_example_data.py` in the repo. The important things to note from this section of code is that our sampling frequency is 149 Hz, our low frequency data is 0.05 Hz, and the high frequency data is 30 Hz.

{% highlight python linenos %}
    # Define properties for each section: 
    section_properties = [
        
        # (num_cycles, amplitude, frequency, noise_percentage)
    
        (5, 0.01, 1, 10),  # Noisy filler data
        (3, 50, 0.05, 0.01),  # Num cycles, high amplitude, low frequency, 1% Gaussian noise
        (3, 0.01, 1, 10),  # Noisy filler data
        (10000, 0.5, 30, 0.02),   # Num cycles, low amplitude, high frequency, 2% Gaussian noise
        (3, 0.01, 1, 10),  # Noisy filler data
        (5, 50, 0.05, 0.01),  # Num cycles, high amplitude, low frequency, 1% Gaussian noise
        (20000, 0.5, 30, 0.02),   # Num cycles, low amplitude, high frequency, 2% Gaussian noise
        (7, 50, 0.05, 0.01),  # Num cycles, high amplitude, low frequency, 1% Gaussian noise
        (5, 0.01, 1, 10),  # Noisy filler data
    ]
    
    # Set the sampling frequency
    sampling_frequency = 149
{% endhighlight %}

## Define Inputs

Here we define some inputs, like the folder to look for the data in and what the different column names are. The important items to note here are the frequency ranges that we specify, and the temporal resolution. The frequency ranges are where we are looking to determine if a frequency is low, high, or anywhere in between that we specify. This is a good example of using generic configuration and structuring your code to read the configuration. When I wrote the original version two years ago, the code could only determine two frequencies, high and low. Now, all we have to do is add another element to this list and the script can label that frequency range as well. The second thing is the temporal resolution. Again, that is how fast we can see changes in frequency. In this case, we can determine if the frequency changes every 0.75 seconds. 

{% highlight python linenos %}
plot_original_data = True  # Plot the original data using Plotly resampler
debug_plots = True  # Plot optional plots to see what the script is doing behind the scenes
plot_all_clusters = True  # Plot the final results

data_folder = r"."
files = glob.glob(os.path.join(data_folder, "example_data*.csv")) 

time_col = "Time"  # The column name for the time
y_col = "Signal"  # The column name for the dependent data to be clustered


frequency_ranges = {  # The different frequency ranges to be identified
    # "name": (min, max) in Hz
    "Low": (0, 5),
    "High": (25, 45)
}

"""
This is how many seconds we want between times we determine the frequency. The smaller this is, the longer the run time.
See the note above. 
"""
temporal_resolution = 0.75  # seconds


"""
Eps is the greediness of the clustering. Larger eps means larger clusters. Too large and all the data will be one 
cluster. Too small and all the data will be considered noise. Min samples is the minimum number of points in the 
spectrogram space (see the dominant frequencies plot in debug mode) for a cluster to be considered a cluster.
"""
clustering_parameters = {"eps": 0.17, "min_samples": 50}
{% endhighlight %}

## Load the Data
Now, we use Polars to load the data, using their pl.scan_csv() functionality. This lazily scans a CSV so that only the required data is read from the file when you finally instruct Polars to actually load the file. In this case, we didn't really have to lazily scan the .csv since there wasn't a lot of data and we weren't doing any preprocessing on it, but that is vestigial from the real implementation. 

{% highlight python linenos %}
df = pl.scan_csv(files, try_parse_dates=True).collect()
{% endhighlight %}

## Plot the Original Data

### Plotly
In this case, we used vanilla Plotly to create a plot of the original data since there weren't too many data points. 

{% highlight python linenos %}
fig = go.Figure()
fig.add_trace(
    go.Scattergl(
        x=x,
        y=y,
        name='Vertical Displacement',
        showlegend=True
    ),
)
fig.show(renderer="browser")
{% endhighlight %}

### Plotly Resampler
If there was a lot of data generated, we would have to use 

{% highlight python linenos %}
fig = go.Figure()
fig.add_trace(
    go.Scattergl(
        x=x,
        y=y,
        name='Vertical Displacement',
        showlegend=True
    ),
)
fig.show(renderer="browser")
{% endhighlight %}

## Take the STFT
There are several inputs to the STFT. The most important of which are the window type, the window size, and the window overlap. I left the window type as the default value provided by scipy, but the window type and the window size were carefully selected to achieve a desired level of temporal accuracy which was specified as an input. The temporal resolution (in seconds) says how many seconds pass between each point that the STFT samples the frequency. Thus, any changes in frequency that happen faster than the temporal resolution will not be detected. It was important to maintain the temporal resolution below the rate at which the data changed from low frequency to high frequency. Per the Uncertainty Principle, the temporal resolution is a trade-off of spectral resolution, so increasing the temporal resolution would lower the spectral resolution, possibly to the point of losing the ability to distinguish between the sections of the signal.

{% highlight python linenos %}
# First determine the sampling frequency (fs)
# Convert to float by dividing a time delta by another TD
fs = datetime.timedelta(seconds=1) / (df[time_col].gather([1])[0] - df[time_col].gather([0])[0])

# Determine the samples per window and window overlap to get the desired temporal resolution
# https://dsp.stackexchange.com/questions/42428/understanding-overlapping-in-stft
# I did the math to determine the formula for nperseg and noverlap Assume we want 12.5% overlap (default from Scipy)
overlap_fraction = 0.125
nperseg = int(temporal_resolution * fs / (1-overlap_fraction))
noverlap = int(overlap_fraction*nperseg)

f, t, Sxx = spectrogram(y, fs, nperseg=nperseg, noverlap=noverlap, scaling="spectrum")

# Zoom the data to the range we care about (below max detection frequency)
max_detection_frequency = np.max(np.array(list(frequency_ranges.values())))
idx = f < max_detection_frequency*1.1
f, Sxx = f[idx], Sxx[idx, :]
{% endhighlight %}

Note, that we also zoomed the data in within the frequency realm. Since our data was sampled higher than the Nyquist frequency, we were able to resolve frequencies higher than we cared about (since we know our highest frequency is 30 Hz). Thus, to make the spectrogram prettier, we just chopped off those higher frequencies that we didn't care about.

## Identify Dominant Frequencies

Next we'll identify the dominant frequency from the spectrogram. If you recall the spectrogram diagram above, that is the portion of the spectrogram with the most energy, which was the most yellow.

{% highlight python linenos %}
dominant_frequencies = f[np.argmax(Sxx, axis=0)]
{% endhighlight %}

## Temporally Cluster the Dominant Frequencies

Here we use the DBCSAN algorithm to identify the clusters from the dominant frequency plot. We use tqdm to create a progress bar as this runs to see how long it takes. Then we create an Sklearn scaling pipeline to scale our data to have 0 mean and unit variance. This wasn't entirely necessary, and the algorithm worked without it, but it can generally help your machine learning algorithms if the data is properly scaled. 

Once we had the clusters identified, we determined the average frequency of that cluster and stored that information for later. 

{% highlight python linenos %}
with tqdm(total=1, desc="Temporally Clustering Dominant Frequencies") as pbar:
    pipeline = make_pipeline(StandardScaler(), DBSCAN(**clustering_parameters))
    X = np.column_stack((t, dominant_frequencies))  # Cluster with time as a factor
    cluster_labels = pipeline.fit_predict(X)

    # Identify the cluster frequencies
    cluster_frequencies = {}
    for cluster_label in sorted(np.unique(cluster_labels)):
        cluster_frequency = np.mean(dominant_frequencies[cluster_labels == cluster_label])
        cluster_frequencies[cluster_label] = cluster_frequency

    # Label the frequency as the correct type
    cluster_types = {}

    for cluster_label, cluster_frequency in cluster_frequencies.items():
        if cluster_label == -1:  # -1 is the noise cluster
            cluster_types[cluster_label] = "Noise"
        else:
            # Search for the first frequency range that the cluster frequency falls in and set that as the label
            for i, (cluster_type, (f_min, f_max)) in enumerate(frequency_ranges.items()):
                if f_min <= cluster_frequency <= f_max:
                    cluster_types[cluster_label] = cluster_type
                    break
                if i == len(frequency_ranges):
                    raise ValueError("A frequency was detected that does not fall within the desired frequency ranges.")

    pbar.update(1)
{% endhighlight %}

## Identify Cluster Start and End Times

We had the data clustered at the temporal resolution of the STFT. Meaning, whereas our original data had a point every 1/149th of a second (inverse of our sampling frequency), the STFT data had a point every 0.75 seconds (the temporal resolution we specified in the beginning). We need to determine the start and end time of each cluster, that way we can go back to the big dataframe of our original data and label the clusters there, since right now we only have the data labeled at the STFT temporal resolution. 

{% highlight python linenos %}
with tqdm(total=1, desc="Labeling Clusters") as pbar:
    cluster_times = {}
    for cluster_label in np.unique(cluster_labels):
        cluster_time = (t[cluster_labels == cluster_label])
        cluster_times[cluster_label] = (np.min(cluster_time), np.max(cluster_time))
{% endhighlight %}

## Label the Original Data

Finally, now that we know what times each cluster starts and ends at, we can go back to the original data and label each point that falls within those clusters. We use the lazy API of Polars since we are looping through the DataFrame once per cluster and we don't want to initiate the calculations until we're done looping. This saves us time by letting Polars optimize the execution behind the scenes. 

{% highlight python linenos %}
    test_start_time = df[time_col].gather([1])[0]  # Get the start time of the test
    df = df.lazy()  # Return to lazy execution mode
    df = df.with_columns(pl.lit(-1).alias("Cluster"))  # Add a cluster label column with the default value of -1
    df = df.with_columns(pl.lit("Noise").alias("Cluster Type"))

    # Loop over each cluster
    for cluster_label, (start_time, end_time) in cluster_times.items():
        # Skip the noise cluster
        if cluster_label == -1:
            continue
        
        # Start and end time are in seconds, referenced to 0. We want them to be relative to the start of the dataframe
        # and as datetime objects so that we can add them to the start time
        cluster_start_time = test_start_time + datetime.timedelta(seconds=start_time)
        cluster_end_time = test_start_time + datetime.timedelta(seconds=end_time)

        # Label data points within the cluster time range with the cluster label
        mask = (pl.col(time_col) >= cluster_start_time) & (pl.col(time_col) <= cluster_end_time)
        df = df.with_columns([
            pl.when(mask).then(pl.lit(cluster_label)).otherwise(pl.col("Cluster")).alias("Cluster"),
            pl.when(mask)
                .then(
                    pl.lit(cluster_types[cluster_label]))
                .otherwise(
                    pl.col("Cluster Type")
                )
                .alias("Cluster Type")
        ])

    df = df.collect()
    pbar.update(1)
{% endhighlight %}

# Conclusion

Again, this project demonstrated the synergy between signal processing techniques like STFT, frequency analysis, and clustering algorithms like DBSCAN. By combining these methods, I successfully transformed and clustered periodic data, providing a comprehensive and insightful representation of the underlying patterns in the original time series.

# Resources

## Data-Driven Science and Engineering
[An excellent book](https://databookuw.com/) provided by Steven Brunton and Nathan Kutz, the online material included here gives a summary of all the requisite concepts to understand the analysis here. The actual PDF of the book is provided by them on their website, [here](https://databookuw.com/databook.pdf). The particularly relevant sections for this analysis are [Chapter 2: Dimensionality Reduction and Transforms](https://databookuw.com/page-2/page-21/), and [Chapter 5: Clustering and Classification](https://databookuw.com/page/page-8/).

## 3Blue1Brown
Created by mathematician Grant Sanderson, [3Blue1Brown](https://www.3blue1brown.com/) is a youtube channel that creates math visualizations and explanations to help people understand complex concepts.

**Relevant Lectures**
- [Fourier Series](https://www.youtube.com/watch?v=r6sGWTCMz2k)
- [Fourier Transform](https://www.youtube.com/watch?v=spUNpyF58BY)
- [Uncertainty Principle](https://www.youtube.com/watch?v=MBnnXbOM5S4)

## Veritasium 
Another science and math youtube channel, this time with a video on the history and implementation of the [Fast Fourier Transform](https://www.youtube.com/watch?v=nmgFG7PUHfo&t=892s)