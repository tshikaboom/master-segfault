# PeerSim

## Configuration file

```java
01 # PEERSIM EXAMPLE 1
02
03 random.seed 1234567890
04 simulation.cycles 30
05
06 control.shf Shuffle
07
08 network.size 50000
09 
10 protocol.lnk IdleProtocol
11
12 protocol.avg example.aggregation.AverageFunction
13 protocol.avg.linkable lnk
14 
15 init.rnd WireKOut
16 init.rnd.protocol lnk
17 init.rnd.k 20
18
19 init.peak example.aggregation.PeakDistributionInitializer
20 init.peak.value 10000
21 init.peak.protocol avg
22
23 init.lin LinearDistribution
24 init.lin.protocol avg
25 init.lin.max 100
26 init.lin.min 1
27
28 # you can change this to select the peak initializer instead
29 include.init rnd lin
30
31 control.avgo example.aggregation.AverageObserver
32 control.avgo.protocol avg
```

## Model
A init control is a Control that runs only in the beginning of the simulation. It is defined by the keyword init

init.`control_name` Java_Class

`control_name` is an _unique arbitrary name_ for the control, `Java_class` is the full Java path of the class.

## Vacancies

 The first things to note are the key names: some of them refer to global properties, while some others refer to single component instances. For example, simulation.cycles is global, but protocol.lnk.xxx defines parameter xxx of protocol lnk.

Observe that each component has a name, such as lnk. In the case of protocols, this name is mapped to a numeric index called protocol ID, by the PeerSim engine. This index does not appear in the configuration file, but it is necessary to access protocols during a simulation. We give more details later.

A component such as a protocol or a control is declared by the following syntax:

```java
 <protocol|init|control>.string_id [full_path_]classname
```

Note that the full class path is optional, in fact PeerSim can search in its classpath in order to find a class. If multiple classes share the same name (in distinct packages), the full path is needed. The prefix init defines an initializer object, that has to implement the Control interface.

The component parameters (if any) follows this scheme:

```java
 <protocol|init|control>.string_id.parameter_name
 ```

For example, at line 10, the first protocol chosen comes to life; the key part contains its type, in this case protocol followed by the name, in this case lnk, and the value part contains the classname for the component, in this case IdleProtocol. This class is in the peersim package, and you don't have to know what is its fully specified name.

Parameters can be declared for each component. For example, see line 13, where the key part contains the parameter name and the value part is simply the value desired.

From line 3 to line 8 some global simulation properties are imposed; these are the total number of simulation cycles and the overlay network size. The Shuffle control (line 6) shuffles the order in which the nodes are visited in each cycle.

From line 10 to line 13, two protocols are put in the arena. The first one, IdleProtocol does nothing. It simply serves as a static container of links to neighboring nodes. The class IdleProtocol does not implement CDProtocol, but it does implement the Linkable interface, through which it provides the links to neighbors.

The second protocol (protocol.avg aggregation.AverageFunction) is the averaging version of aggregation. Its parameter (linkable) is important: the aggregation protocol needs to talk to neighbors but doesn't have its own list of neighbors. In a modular fashion, it can be put over any overlay network. The protocol (array) that defines the overlay network has to be specified here. The value of parameter linkable is the name of a protocol implementing the Linkable interface (IdleProtocol in the example).

From line 15 to line 26, it is time to initialize all the components previously declared. We declare three initialization components, but only two of them are actually used (see line 29). The first initializer, peersim.init.WireKOut, performs the wiring of the static overlay network. In particular, the nodes are linked randomly to each-other to form a random graph having the specified degree (k) parameter.

The second and third initializers are alternatives to initialize the aggregation protocol, in particular, the initial values to be averaged. The initializers set the initial values to follow a peak or linear distribution, respectively. Peak means that only one node will have a value different from zero. Linear means that the nodes will have linearly increasing values. Both initializers need a protocol name that identifies the protocol to initialize (protocol parameter). Additional parameters are the range (max, min parameters) for the PeakDistributionInitializer and value parameter for LinearDistribution.

The choice to use the peak or linear distribution is given by the include.init property (line 29) that selects which initializers are allowed to run. This property also defines the order in which the components are run. Note that the default order (if there is no include property) is according to alphabetical order of names. A similar include property works also with protocols and controls.

Finally at line 31, 32 the last component is declared: aggregation.AverageObserver. Its only parameter used is protocol which refers to the aggregation.AverageFunction protocol type, so the parameter value is avg.

Now you can run the example writing on a console the following line:

```bash
java -cp <class-path> peersim.Simulator example1.txt
```

# TME1

> Question 1 Créez dans votre projet Eclipse un package tme1 et copiez-y dans le dossier correspondant les fchiers fournis dans vos ressources  TME. Rafraîchissez et assurez-vous que la compilation se passe bien (absence de croix rouge)

ok

> Question 2 Les fchiers qui vous ont été fournis implémentent un protocole applicatif (`ApplicativeProtocol.java`) qui se base sur des primitives de difusion. Déterminez ce que fait ce protocole

Broadcasts `plus` and `fois`. Either adds or multiplies, broadcasts sends out all the messages.

> Question 3 Analysez le code qui vous a ete fourni et produisez un fichier de configuration `peersim` de maniere a d'avoir une graine aleatoire a la valeur 5 

`random.seed 5`

>ce que le protocole applicatif fonctionne sur 5 noeurs en se basant sur le protocol de diffusion basique (`BasicBroadcast.java`)

```bash
SIZE 5
network.size SIZE
protocol.emitterProtocol BasicBroadcast
```

> utiliser une couche transport fiable (prendre `UniformRandomTransport` de l'API `peersim`) et d'avoir une latence reseau comprise entre 5 et 500 unites de temps 

```cfg
# parameters of message transfer
# delay values here are relative to cycle length, in percentage,
# eg 50 means half the cycle length, 200 twice the cycle length, etc.
MINDELAY 500
MAXDELAY 50000
```

> que le module d'initialisation soit la classe `Initialisation` 

> que le controleur `EndControler` soit execute une fois la simulation terminee 

> que le temps de simulation soit suffisamment grand pour que le protocole applicatif se termine

> Question 4 Testez la meme experience en simulant des pannes franches de noeud a l'emission. Pour cela utilisez la classe `DeadlyTransport` en parametrans que les noeuds `1` et `3` peuvent tomber en panne avec une probabilite de `0.1`. Notez les resultats verifiez que le broadcast ne respecte pas la specification d'un broadcast fiable

> Question 5 Testez la meme experience en appliquant cette fois ci le protocole de diffusion fiable (`ReliableBroadcast` dont l'algorithme est donne en cours) sur le protocole de transport non fiable. Notez les resultats et verifiez que la specification du broadcast fiable est respectee. 

> Question 6 En respectant les meme mecanismes que le code qui vous a ete fourni 

> codez une classe de broadcast (implementant l'interface `broadcast`) qui implemente l'algorithme de diffusion FIFO presente en cours. Cette classe se basera sur un protocole de broadcast qui sera specifie dans le fichier de configuration

> testez votre implementation dans un milieu fiable (pas de pannes franches)

> Verifiez que les affichage correspodantent bien a une diffusion FIFO.

> Question 7 Meme question mais en implementant une diffusion causale qui se basera sur un protocole de diffusion FIFO. Notez les resultats.

> Question 8 Creez une classe de broadcast qui implemente l'algorithme de diffusion total a sequenceur fixe. Testez cette classe en vous basant sur un transport fiable et le broadcast basique et verifiez que les valeurs de chaque replicas sont egales. L'identifiant du sequenceur pourra etre parametrable dans le fichier de configuration. 

> Question 9 Testez votre code avec plusieurs combinaisons de protocole

>> `reliableTransport + reliableBroadcast + FIFObroadcast + applicativeProtocol`

>> `reliableTransport + reliableBroadcast + FIFObroadcast + causalbroadcast + applicativeProtocol`

>> `reliableTransport + reliable broadcast + total broadcast + applicative protocl`

>> `reliable Tranport + reliable broadcast + total broadcast + fifo broadcast + applicative protocl`
