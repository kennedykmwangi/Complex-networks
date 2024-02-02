# Complex-networks
Comparison of different network models
import networkx as nx
import matplotlib.pyplot as plt
import numpy as np
import collections
import random

def task1():

    #########task1: comparison of different network models (WS, BA, G(N,p), G(N,L))
    N = 1000 # 4 nodes, was 1000
    p=4/(N-1)
    # YOUR CODE HERE
    #G = nx.Graph() #empty graph
    #G.add_edges_from([(1, 2), (1, 3), (1,4), (3,4)]) #numbers are just labels
    G = nx.erdos_renyi_graph(N,p) #Task 1, a)
    title = 'G(N,p) p='+str(p)    # YOUR CODE HERE

    # degree distribution K1 =3, K2=2, K3=2, K4=2 , possible degrees 1 to 3, degree= number of connections going thorugh nodes ( 1 adet 1 - 2 adet 2 - 1 adet 3)
    degrees = collections.Counter(dict(G.degree).values())

    plt.figure()
    plt.vlines(list(degrees.keys()), 0, [v / N for v in degrees.values()], '#99FFFF') #vlines = vertical lines
    plt.plot(list(degrees.keys()), [v / N for v in degrees.values()], 'o')
    plt.title(title, fontsize=20)
    plt.xlabel("degree", fontsize=15)
    plt.ylabel("distribution", fontsize=15)
    plt.show()

    # average degree
    average_degree = sum([k * v for k, v in degrees.items()]) / sum(degrees.values())
    print("avg_degr")
    print(average_degree)

    # cluster coefficients distribution #
    clust_coefs = list(nx.algorithms.cluster.clustering(G).values())
    #print("clust_coefs")
    #print(clust_coefs)
    plt.figure()
    plt.hist(clust_coefs, density=True)
    plt.title(title, fontsize=20)
    plt.xlabel("clustering coefficient", fontsize=15)
    plt.ylabel("distribution", fontsize=15)
    plt.show()

    # average cluster coefficient
    av_clust_coef = np.mean(clust_coefs)
    av_clust_coef = nx.algorithms.cluster.average_clustering(G)
    print(av_clust_coef)

    # or the same result:
    #av_clust_coef = nx.algorithms.cluster.average_clustering(G)

    nodes=max(nx.connected_components(G), key=len,default=[])
    G=G.subgraph(nodes).copy()
    # diameter
    diameter = nx.diameter(G)
    print(diameter)
    # average shortest path length
    average_path_length = nx.average_shortest_path_length(G)
def check_robustness1(G, f_vec):
    # calculate the giant cluster size of the original graph
    P0 = len(max(nx.connected_components(G), key=len, default=[]))
    # sort nodes with respect to their degrees. Nodes with equal degree are returned in random order
    sorted_nodes = list(zip(*sorted(G.degree, key=lambda x: x[1]+random.random(), reverse=True)))[0]
    # initialize relsize_vec to an empty list
    relsize_vec = []
    #for each fraction f given in the argument f_vec
    for f in f_vec:
        # make a copy of the original graph
        G_temp = nx.Graph(G)
        # calculate the number of nodes to remove from the fraction of nodes f
        f0 = int(f * len(G.nodes))
        # select the nodes to remove, first f0 elements from the list sorted_nodes
        nodes_to_remove = random.sample(G.nodes, f0)
        # remove the nodes from the copied graph
        G_temp.remove_nodes_from(nodes_to_remove)
        # calculate the giant cluster size of the graph with deleted nodes
        Pf = len(max(nx.connected_components(G_temp), key=len, default=[]))
        # store the relative giant cluster size in the list relsize_vec
        relsize_vec.append(Pf / P0)
    return relsize_vec
    # example usage: plt.plot([0, 0.5, 1], check_robustness2(G, [0, 0.5, 1]))

    # YOUR CODE HERE

# function for method from task 2: removing nodes with highest degree
def check_robustness2(G, f_vec):
    # calculate the giant cluster size of the original graph
    P0 = len(max(nx.connected_components(G), key=len, default=[]))
    # sort nodes with respect to their degrees. Nodes with equal degree are returned in random order
    sorted_nodes = list(zip(*sorted(G.degree, key=lambda x: x[1]+random.random(), reverse=True)))[0]
    # initialize relsize_vec to an empty list
    relsize_vec = []
    #for each fraction f given in the argument f_vec
    for f in f_vec:
        # make a copy of the original graph
        G_temp = nx.Graph(G)
        # calculate the number of nodes to remove from the fraction of nodes f
        f0 = int(f * len(G.nodes))
        # select the nodes to remove, first f0 elements from the list sorted_nodes
        nodes_to_remove = sorted_nodes[:f0]
        # remove the nodes from the copied graph
        G_temp.remove_nodes_from(nodes_to_remove)
        # calculate the giant cluster size of the graph with deleted nodes
        Pf = len(max(nx.connected_components(G_temp), key=len, default=[]))
        # store the relative giant cluster size in the list relsize_vec
        relsize_vec.append(Pf / P0)
    return relsize_vec
    # example usage: plt.plot([0, 0.5, 1], check_robustness2(G, [0, 0.5, 1]))

    # YOUR CODE HERE
######################## task 2 : checking the robustness of the network
# def check_robustness1(G, f_vec):
    # YOUR CODE HERE
def task2():
    ff=np.linspace(0,1,5)  #increase 5 later
    N = 1000  # 4 nodes, was 1000
    p = 4 / (N - 1)
    G = nx.erdos_renyi_graph(N, p) # repeat it for other graphs
    title = 'G(N,p) p='+str(p)    # YOUR CODE HERE
    P_errors = check_robustness1(G, ff)
    P_attacks = check_robustness2(G, ff)

    plt.figure()
    plt.plot(ff, P_errors, 'or', label="errors")
    plt.plot(ff, P_attacks, '.b', label="attacks")
    plt.legend()
    plt.title(title, fontsize=20)
    plt.xlabel("fraction of nodes removed", fontsize=15)
    plt.ylabel("rel.giant cluster size", fontsize=15)
    plt.show()
    
def task3a():
    bb=np.logspace(-4, 0, 14)
    print(bb)
    N=100
    K=4
    G0=nx.watts_strogatz_graph(N, K, 0)#regular graph
    av_clust_coef0 = nx.algorithms.cluster.average_clustering(G0) #coef0 for G0
    average_path_length0 = nx.average_shortest_path_length(G0)
    C= []
    d =[]
    for b in bb:
        G = nx.watts_strogatz_graph(N, K, b)
        av_clust_coef = nx.algorithms.cluster.average_clustering(G)  # get rid off 0's in naming
        average_path_length = nx.average_shortest_path_length(G)

        C.append(av_clust_coef/av_clust_coef0) #add this to C list defined above
        d.append(average_path_length/average_path_length0) #add this to l list defined above
    plt.figure()
    plt.semilogx(bb, C, 'or', label='rel. cl') #logarithimic plot, C ;small o = circle and red
    plt.semilogx(bb, d, '.b', label="rel. av. sh. path len") #logaritmik plot, l  ; . and blue
    plt.legend()
    plt.xlabel("beta", fontsize=15)
    plt.ylabel("av. sh. path len.", fontsize=15)
    plt.title("WS(N="+str(N)+" K="+str(K)+")")
    plt.show()

def task3b():
    N_vec = [10, 30, 100, 300, 1000]

    K=4
    d1=[]
    d2=[]
    dt=[]

    for N in N_vec:
       G1 = nx.watts_strogatz_graph(N, K, 0.1) #beta = 0.1
       G2 = nx.watts_strogatz_graph(N, K, 0.9) #beta = 0.9

       d1.append(nx.average_shortest_path_length(G1)) #add this to l1 list defined above
       d2.append(nx.average_shortest_path_length(G2)) #add this to l2 list defined above

       dt.append(np.log(N)/np.log(K)) #from notes


    plt.figure()
    plt.semilogx(N_vec, d1, 'or', label='beta=0.1')  # logarithimik plot, C ;small o = circle and red
    plt.semilogx(N_vec, d2, '.b', label="beta=0.9")  # logarithimik plot, l  ; . and blue
    plt.semilogx(N_vec, dt, '-k', label="theory for random graph")
    plt.legend()
    plt.xlabel("beta", fontsize=15)
    plt.ylabel("av. sh. path len.", fontsize=15)    
    plt.title("WS(N="+str(N)+" K="+str(K)+")")
    plt.show()

    
if __name__ == '__main__':
    task3a()
