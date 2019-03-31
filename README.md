# Build Speech Enhancement Dataset

Build speech enhancement dataset using TIMIT and NoiseX92 corpus.

## Dependencies 

- tqdm
- librosa

## Usage

Clone repository, the repository contains **most of the NoiseX-92 corpus** and needs patience:

```shell
git clone https://github.com/haoxiangsnr/Build-SE-Dataset.git 
```

Download TIMIT Corpus from https://github.com/philipperemy/timit

Put it in the `./data/TIMIT` directory, and extract it:

```shell
cd Build-SE-Dataset
sudo apt install unzip
unzip data/TIMIT/TIMIT.zip -d data/TIMIT
```

The directory structure is as follows：

```shell
data
├── NoiseX92
│   ├── babble.wav
│   ├── buccaneercockpit1.wav
│   ├── buccaneercockpit2.wav
│   ├── destroyerengine.wav
│   ├── destroyerops.wav
│   ├── f16.wav
│   ├── factoryfloor1.wav
│   ├── factoryfloor2.wav
│   ├── hfchannel.wav
│   ├── leopard.wav
│   ├── m109.wav
│   ├── machinegun.wav
│   ├── pinknoise.wav
│   ├── volvo.wav
│   └── whitenoise.wav
└── TIMIT
    ├── data
    └── TIMIT.zip
```

Configuring `./config.json`

- If the same `release_dir` is specified, `release_dir` will be cleared first and then generated again in `release_dir`
- Use `minimum_sampling` to specify the minimum number of samples, and the TIMIT corpus that meets the requirements will be used (Default sr = 16000).

```json
{
    "release_dir": "../release_timit",
    "minimum_sampling": 16384,
    "num_of_utterances": 10,
    "split": 0.9, # Split utterances into training and test sets
    "train": {
        "dbs": [0, -5, -10, -15],
        "noise_types": ["babble", "destroyerengine", "destroyerops", "factoryfloor1"]
     },
    "test": {
        "dbs": [-20, -17, -15, -12, -10, -7, -5, 3, 0, 5, -3],
        "noise_types": ["babble", "destroyerengine", "destroyerops", "factoryfloor1", "factoryfloor2"]
     }
}
```


Build speech enhancement dataset:

```shell
[Build-SE-Dataset] python main.py 
Loading wav files ...
Wav file: 1
Wav file: 2
...
Wav file: 10
The total number of wav files in the training set is: 9.
The total number of wav files in the test set is: 1. 

Building training set:
Loading noises: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 4/4 [00:16<00:00,  4.26s/it]
Add noise for clean waveform: 9it [00:00, 61.20it/s]
Build training dataset finished, the result is stored in ../release_0_-5_-10_-15_810_90/train. 

Building test set:
Loading noises: 100%|███████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████████| 5/5 [00:20<00:00,  4.05s/it]
Add noise for clean waveform: 1it [00:00, 19.26it/s]
Build test dataset finished, the result is stored in ../release_0_-5_-10_-15_810_90/test. 

Build SE dataset finished, the result is stored in ../release_0_-5_-10_-15_810_90. 

You can use command line to transfer release data to remote dir: 
         time tar -c <local_release_dir> | pv | lz4 -B4 | ssh user@ip "lz4 -d | tar -xC <remote_dir>"
```

The dataset is as follows:

```shell
release_timit/
├── test
│   ├── clean
│   │   ├── 0001_babble_0.wav
│   │   ├── 0001_babble_-10.wav
│   │   ├── ...
│   │   └── 0001_factoryfloor2_-7.wav
│   └── noisy
│       ├── 0001_babble_0.wav
│       ├── 0001_babble_-10.wav
│       ├── ...
│       └── 0001_factoryfloor2_-7.wav
├── test.npy # {"0001_babble_0": {"noisy": noisy_y, "clean": clean_y}, ...}
├── train
│   ├── clean
│   │   ├── 0001_babble_0.wav
│   │   ├── 0001_babble_-10.wav
│   │   ├── ...
│   │   └── 0009_factoryfloor1_-5.wav
│   └── noisy
│       ├── 0001_babble_0.wav
│       ├── 0001_babble_-10.wav
│       ├── ...
│       └── 0009_factoryfloor1_-5.wav
└── train.npy # {"0001_babble_0": {"noisy": noisy_y, "clean": clean_y}, ...}