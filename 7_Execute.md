# 7 Execute The System In The Simulator

Now that everything is build together we can execute the system in the simulator.

### 1 Start the simulator
```
${CLFS}/eco32/build/bin/sim -m 32 -s 2 -t 0 -t 1 -g -d ${CLFS}/disk.img -r ${CLFS}/eco32/build/monitor/simulator/monitor.bin -i
```