# Python
Coding
import numpy as np
import matplotlib.pyplot as plt
import glob
import os

files_path_1 = r'C:\cygwin64\home\result_copy'
files_path_2 = r'C:\cygwin64\home\result_copy'
f_array = []
dist_array = []

f_pattern = 'slow_pullf*.xvg'
dist_pattern = 'slow_pullx*.xvg'
f_file_list = glob.glob(os.path.join(files_path_1, f_pattern))
print(f_file_list)
dist_file_list = glob.glob(os.path.join(files_path_2, dist_pattern))
print(dist_file_list)

for file in f_file_list:
    force_data = np.loadtxt(file, skiprows=17, usecols=(1), max_rows=79000)
    f_array.append(force_data)

for file in dist_file_list:
    dist_data = np.loadtxt(file, skiprows=17, usecols=(1), max_rows=79000)
    dist_array.append(dist_data)

# Print sizes for debugging
print("Size of f_array:", sum(len(arr) for arr in f_array))
print("Size of dist_array:", sum(len(arr) for arr in dist_array))

def calculate_average_forces(dist_array, f_array):
    num_bins = 1000
    dist_array = np.concatenate(dist_array)
    f_array = np.concatenate(f_array)
    bin_width = (np.max(dist_array) - np.min(dist_array)) / 1000
    bins = np.linspace(dist_array.min(), dist_array.max(), num_bins + 1)
    bin_indices = np.digitize(dist_array, bins)

    average_forces = np.zeros(num_bins)
    count_per_bin = np.bincount(bin_indices, minlength=num_bins + 1)

    for i in range(1, num_bins + 1):
        bin_mask = (bin_indices == i)
        if np.any(bin_mask):
            print(f"Size of f_array[bin_mask]: {len(f_array[bin_mask])}")
            average_forces[i - 1] = np.mean(f_array[bin_mask])

    return bins, average_forces

def plot_results(bins, average_forces):
    plt.plot(bins[:-1], average_forces, label='Average Force')
    plt.xlabel('Distance')
    plt.ylabel('Average Force')
    plt.title('Force vs Distance Binned Averaging')
    plt.legend()
    plt.show()

# Calculate average forces and plot the results
bins, average_forces = calculate_average_forces(dist_array, f_array)
plot_results(bins, average_forces)
