# "E: Unable to locate package python3-pip"

```python
sudo add-apt-repository universe
sudo apt-get update
```

Which is enabling the category `universe`

```python
deb http://archive.ubuntu.com/ubuntu bionic main universe
deb http://archive.ubuntu.com/ubuntu bionic-security main universe
deb http://archive.ubuntu.com/ubuntu bionic-updates main universe
```

OR

```python
sudo apt-get update
sudo apt-get install -y python3-pip
```

