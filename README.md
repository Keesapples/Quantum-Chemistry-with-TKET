# Quantumistry
# Title: World-Class Quantum Chemistry with TKET

Contribution: : TAM S.K. (70%), Bram D. (30%), Lourens B., Costantin D. (Until 23-Aug)

# Introduction
Variational Quantum eigensolver (VQE) algorithm is commonly used in computing energy of molecule using quantum devices. To implement a VQE algorithm, a molecular with a given configuration is firstly used to constructed a problem. After applying mapping techniques and ansatz generation approach, the problem can then transformed to a parameterized ansatz circuit. With the help of optimzer, the VQE repeating measuring and updaing the parameters of the ansatz circuit. The final value is obtained when the measured value is converged.

The basic information of open backends are summarized as follows [1-3]:

| Item | Provider | Machine Name | Qubits | Gate |
| :-: | :-: | :-: | :-: | :-: |
| 1 | IBM | ibmq_lima | 5 | Rz, U3, U2, U1, CX, SX, X, noop, Measure, Reset |
| 2 | IBM | ibmq_belem | 5 | Rz, U3, U2, U1, CX, SX, X, noop, Measure, Reset |
| 3 | IBM | ibmq_quito | 5 | Rz, U3, U2, U1, CX, SX, X, noop, Measure, Reset |
| 4 | IBM | ibmq_manila | 5 | Rz, U3, U2, U1, CX, SX, X, noop, Measure, Reset |
| 5 | IBM | ibm_nairobi | 7 | Rz, U3, U2, U1, CX, SX, X, noop, Measure, Reset |
| 6 | IBM | ibm_oslo | 7 |Rz, U3, U2, U1, CX, SX, X, noop, Measure, Reset |
| 7 | IonQ | Harmony | 11| GPi, GPi2, Virtual RZ, MS |
| 8 | Quantinuum | HI-2 | 12 | U1q, Rz, ZZ |
| 9 | Rigetti | Aspen-11 | 8 | Rx, Rz, CPHASE, CZ, XY |


Using IBMQ LIMA as example, the circuit map is :

<img width="250" alt="Screenshot 2022-08-22 at 7 43 58 PM" src="https://user-images.githubusercontent.com/87143861/185913905-90c3a828-e979-4de9-a838-fb1b5e8edbb5.png">

By utilizing the Noise and the Quantum Circuit Model in pytket library[4], at a certain testing moment, the error of single and entangled gates are as follows:
|Node|I gate|Rz gate|SX gate|X gate|Reset gate|average error|
|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
|0|0.00045564669056089876|0|0.00045564669056089876|0.00045564669056089876|0|0.0002733880143365393|
|1|0.0007931341051846768|0|0.0007931341051846768|0.0007931341051846768|0|0.00047588046311080603|
|2|0.0004822790390659032|0|0.0004822790390659032|0.0004822790390659032|0|0.0002893674234395419|
|3|0.00025647753321282705|0|0.00025647753321282705|0.00025647753321282705|0|0.00015388651992769622|
|4|0.0006932411720766167|0|0.0006932411720766167|0.0006932411720766167|0|0.00041594470324597|

Average single gate error: 0.000322

For CX-gate
|Control gate|Target gate|error|
|:-:|:-:|:-:|
|4|3|0.01175674116384029|
|3|4|0.005878370581920145|
|2|3|0.013302220876095505|
|3|2|0.006651110438047753|
|2|1|0.022572084465386333|
|1|2|0.011286042232693166|
|0|1|0.026409836177538337|
|1|0|0.013204918088769169|

Average entangled gate error: 0.02046

In IBM LIMA, the average single gate and average entagled gate fidelity are 0.999678 and 0.97954. Comparing single gate fidelity, that of entangled gate is always lower. Thus, for circuit implementation, the controlled gate should be minimized. Similar studies are applied on other backends, properties backends can be characterized by the fidelity parameters, including single gate, entangled gate and SPAM (state preparation and measurement) fidelity [5]. The data is shown as follows:

![Fidelity](https://user-images.githubusercontent.com/87143861/185754285-048b1303-aaa9-45b3-97d9-2532a3dcfc85.png)


To test the circuit on various backend, it is desired to have a designed toolkit for compiling and optimizing quantum programs. TKET can help to convert a quantum circuit with standard quantum gate to a composite of gates that can be processable in the operating backend. For example, the quantinuum backend did not have single CNOT gate. However, it can re-constructued by a combination of U1q, ZZ, Rz as shown as follows:

<img width="698" alt="Screenshot 2022-08-20 at 11 55 17 PM" src="https://user-images.githubusercontent.com/87143861/185755674-6455537a-f074-447d-ab57-ef5616e6bcd7.png">
TKET is especially useful for ansatz reconstruction. It can use for optimizing the anstaz for backend with a imprefection of gate fidelity.

Without efficient error correction algorithm, for problems with small number of qubits, it is prefered to have a backend with the highest fidelity on both single and entangled gate. 

# VQE demostration
Selected mappers, ansatz generator and Opimizer are used for testing the performance of the backend.
|FermionicOp Mappers|Ansatz for Fermion|Optimizer|
|:-:|:-:|:-:|
|JordanWignerMapper|UCCSD|COBYLA|
|BravyiKitaevMapper|PUCCD|SPSA|
|ParityMapper|SUCCD|SLSQP|

## H2 simulation

For the VQE using ParityMapper as Mapper, UCCSD as ansatz and SLSQP as optimizer. The ansatz is shown as follows:

<img width="874" alt="Screenshot 2022-08-22 at 11 29 26 PM" src="https://user-images.githubusercontent.com/87143861/185959741-b89f0a8d-1d12-4009-89c3-84fdecd9e9b1.png">

When the solution is simulated by a perfect backend , the answer is as follows:

<img width="402" alt="Screenshot 2022-08-22 at 11 24 30 PM" src="https://user-images.githubusercontent.com/87143861/185958718-46a44b3c-7133-446e-a252-ea7a50ce77da.png">

## LiH simulation

Similarly, for the VQE using ParityMapper as Mapper, UCCSD as ansatz and SLSQP as optimizer. When a perfect backend will give the following result:

<img width="393" alt="Screenshot 2022-08-22 at 11 39 16 PM" src="https://user-images.githubusercontent.com/87143861/185962331-dcbb6326-0e97-46fc-86f1-f5f030ce3eca.png">

# Selected case studied for H2 simulation

To simulate the performance of backend, backend emulator is used. IBMQ LIMA and Syndey is used as example. According to literature, engtangled fidelity of LIMA and Syndey are 0.9898 and 0.9487. The result of VQE simulation as follows:

<img width="450" alt="Screenshot 2022-08-24 at 3 16 22 AM" src="https://user-images.githubusercontent.com/87143861/186246616-43d628a8-fb8c-46ca-add5-21d9d9339e81.png">

The error of the simulation increased when the fidelity of the backend is low. Hence, it is expected the backend with high fidelity will provide a more acuracy result. The quatinuum backend should have the best performance.

# Conclusion
A simple study on VQE is performed. The gate and fidelity of different backends are analyzed. Different Mapper, ansatz and optimzer are tested. One of the optimized combination is using ParityMapper as Mapper, UCCSD as ansatz and SLSQP as optimizer. This combination is applied on backend emulator and simulate the performance of backend. Similar strategy can be applied on other backend.  

# Reference

[1] https://ionq.com/docs/getting-started-with-native-gates#when-to-use-native-gates

[2] https://assets.website-files.com/617730fbcf7b7c387194556a/626bf5510a4489116ba9c430_Quantinuum%20System%20Model%20H1%20Product%20Data%20Sheet%20v8%2027APR22.pdf

[3] https://arxiv.org/pdf/2203.03816.pdf

[4] https://cqcl.github.io/pytket/manual/manual_noise.html

[5] Pelofske, E., Bärtschi, A., & Eidenbenz, S. (2022). Quantum volume in practice: What users can expect from NISQ devices. arXiv preprint arXiv:2203.03816.
