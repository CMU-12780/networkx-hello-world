# NetworkX Hello World

A small walking network of five CMU buildings. You build the graph, find the fastest route between two buildings, analyze the network and package the script as a container.

## Activity

1. Fork this repository.
2. On your fork's **Actions** tab, enable workflows. GitHub turns them off on forks.
3. Fill in the blanks marked `YOUR CODE HERE` and `TODO` in `cmu_campus_network.py`. The tests in `test_cmu_campus_network.py` run on every push and fail until Part 3 is filled in.
4. At the bottom of the script, pick one of the three challenges.
5. Add a GitHub Actions workflow that builds a container image and pushes it to the GitHub Container Registry (GHCR). See [Build and Publish a Container](#build-and-publish-a-container).
6. Submit the link to your repository on Canvas. Grading uses your workflow runs and your container image.

## NetworkX Reference

### 1. Graph Creation
```python
G = nx.Graph()  # Undirected graph
G.add_nodes_from(buildings)  # Add nodes
G.add_edge(node1, node2, weight=time)  # Add weighted edges
```

### 2. Shortest Path Finding
```python
# Find the fastest route
path = nx.shortest_path(G, start, end, weight='weight')
distance = nx.shortest_path_length(G, start, end, weight='weight')
```

### 3. Network Analysis
```python
# Centrality: which building is most "central"?
betweenness = nx.betweenness_centrality(G, weight='weight')

# Degree: how many connections does each node have?
degree = G.degree(node)

# Average path length: average walking time between any two buildings
avg = nx.average_shortest_path_length(G, weight='weight')
```

### 4. Visualization
```python
pos = nx.spring_layout(G)  # Position nodes
nx.draw_networkx_nodes(G, pos)  # Draw nodes
nx.draw_networkx_edges(G, pos)  # Draw edges
nx.draw_networkx_labels(G, pos)  # Add labels
```

### Run the Complete Analysis
```bash
python cmu_campus_network.py
```

## Build and Publish a Container

### 1. Write a Dockerfile

Put a `Dockerfile` in the root of the repository. The one in `.devcontainer/` sets up a development environment and does not run the script. Yours does the following:

* Starts from `python:3.12-slim`.
* Installs `requirements.txt`.
* Copies `cmu_campus_network.py` into the image.
* Runs the script with `CMD`.

Build and run it locally before you write the workflow:

```bash
docker build -t networkx-hello-world .
docker run --rm networkx-hello-world
```

### 2. Write the Workflow

Add a second workflow in `.github/workflows/` that runs on pushes to `main`. It checks out the code, signs in to `ghcr.io` with `docker/login-action` and builds and pushes the image with `docker/build-push-action`.

The workflow's `GITHUB_TOKEN` is read-only by default. Grant it permission to publish packages at the top of the workflow:

```yaml
permissions:
  contents: read
  packages: write
```

Image names must be lowercase, and GitHub usernames are often mixed case. `docker/metadata-action` builds a lowercase image name and tags for you. Pass its `tags` output to `docker/build-push-action`.

### 3. Make the Image Public

GHCR packages start out private. On your GitHub profile, open **Packages**, choose the image, then **Package settings**, and change its visibility to public so it can be pulled for grading.

### 4. Pull and Run It

Replace `your-username` with your GitHub username in lowercase:

```bash
docker pull ghcr.io/your-username/networkx-hello-world:main
docker run --rm ghcr.io/your-username/networkx-hello-world:main
```

You can also pull and run the image from Docker Desktop.
