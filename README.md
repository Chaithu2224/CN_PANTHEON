# CN_PANTHEON

# TCP Congestion Control Simulation using Pantheon on Ubuntu VM

This project simulates and compares congestion control algorithms (BBR, Cubic, and Vegas) using the [Pantheon framework](https://github.com/StanfordSNR/pantheon) inside a VirtualBox Ubuntu environment.

---

## System Requirements

- **Host OS:** Windows / Mac / Linux
- **Virtualization:** [VirtualBox](https://www.virtualbox.org/)
- **Guest OS:** Ubuntu 20.04 or 24.04 (recommended)
- **RAM:** ≥ 4 GB
- **Storage:** ≥ 20 GB free

---

## Software to Install (Inside Ubuntu VM)

### 1. System Update

```bash
sudo apt update && sudo apt upgrade -y
```

---

### 2. Install Dependencies

```bash
sudo apt install -y build-essential g++ curl iperf iproute2 \
    git libprotobuf-dev protobuf-compiler libtool \
    autotools-dev automake pkg-config python2.7-dev \
    libboost-all-dev python-setuptools
```

---

### 3. Install Python 2.7.18 (from source)

```bash
cd /tmp
wget https://www.python.org/ftp/python/2.7.18/Python-2.7.18.tgz
tar -xf Python-2.7.18.tgz
cd Python-2.7.18
./configure --prefix=/opt/python2.7
make -j$(nproc)
sudo make install
sudo ln -s /opt/python2.7/bin/python2.7 /usr/bin/python
```

### 4. Install pip2 and required Python packages

```bash
curl https://bootstrap.pypa.io/pip/2.7/get-pip.py --output get-pip.py
python get-pip.py
pip install --user pyyaml==5.1.2 matplotlib
```

---

## Mahimahi Setup (from ravinet fork)

```bash
cd ~
git clone https://github.com/ravinet/mahimahi.git
cd mahimahi
sudo apt install -y libdbus-1-dev libglib2.0-dev libwebkit2gtk-4.0-dev
./autogen.sh
./configure
make -j$(nproc)
sudo make install
```

️ Verify:
```bash
which mm-link
which mm-delay
which mm-tunnelserver
```

---

## Pantheon Setup

```bash
cd ~
git clone https://github.com/StanfordSNR/pantheon.git
cd pantheon
git submodule update --init --recursive
```

Build Pantheon:
```bash
./tools/install_deps.sh
PYTHONPATH=src python src/experiments/setup.py --setup --all
```

If `copa.py` gives errors, edit `src/config.yml` to exclude `copa`, `taova`, etc.

---

## Add Trace Files

Place trace files in the `tests/` directory:

- `12mbps_data.trace`
- `12mbps_ack.trace`

Or generate with your custom trace generator.

---

## Run Tests

```bash
cd ~/pantheon
python src/experiments/test.py local \
  --schemes "bbr cubic vegas" \
  --run-times 1 \
  --runtime 30 \
  --data-dir output/sample_test \
  --uplink-trace tests/12mbps_data.trace \
  --downlink-trace tests/12mbps_ack.trace \
  --prepend-mm-cmds "mm-delay 5"
```

---

## Analyze Results

```bash
cd ~/pantheon
python src/analysis/analyze.py --data-dir output/sample_test
```

This will generate a PDF and plots inside the same folder.

---

## Extra Notes

- Enable IP forwarding if prompted:

```bash
sudo sysctl -w net.ipv4.ip_forward=1
```

- If `run_first()` errors occur, check each wrapper (e.g., `bbr.py`) and use absolute paths.

---

## Output Files

All logs, plots, and PDF results will be saved in the specified `--data-dir`, such as:

```
output/sample_test/
├── bbr_cc_run1.log
├── cubic_cc_run1.log
├── *.pdf
└── *.png
```

---

## Author

**Sai Chaithanya Teja Salkapuram**  
Pantheon TCP Simulation – Computer Networks Assignment

