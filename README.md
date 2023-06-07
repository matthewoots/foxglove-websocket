# FOXGLOVE_WEBSOCKET
Building the websocket library for `foxglove`, this has been tested on `Ubuntu 20.04`

# SETUP
```
git clone git@github.com:matthewoots/foxglove-websocket.git --recursive
cd <foxglove-websocket>
pushd dependencies/websocketpp/
mkdir build && cd build
cmake ..
sudo make install
popd
mkdir build && cd build
cmake ..
sudo make install
```