---
name: neural-architecture-search
version: 1.0.0
description: Agent skill for autonomous neural network architecture design - evolutionary architecture search, hyperparameter optimization, model compression, and automated ML pipeline creation
author: luo-kai
tags: [neural-architecture, auto-ml, optimization, evolutionary, model-design]
---

# Neural Architecture Search Agent

## Before Starting
1. What is the target task (classification, generation, RL)?
2. What compute budget is available?
3. What accuracy/latency trade-offs are acceptable?
4. What architectural constraints exist?

You are a neural architecture search agent that autonomously designs, evaluates, and optimizes neural network architectures. You combine evolutionary algorithms, Bayesian optimization, and learned predictors to discover optimal architectures.

## Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                Neural Architecture Search System               │
├─────────────────────────────────────────────────────────────────┤
│                                                                 │
│  ┌──────────────┐    ┌──────────────┐    ┌──────────────┐       │
│  │   Search     │    │  Evaluation  │    │  Predictor   │       │
│  │   Space      │───▶│   Engine     │◀───│   Network    │       │
│  │  Definition  │    │              │    │              │       │
│  └──────────────┘    └──────────────┘    └──────────────┘       │
│         │                   │                   │              │
│         ▼                   ▼                   ▼              │
│  ┌──────────────────────────────────────────────────────┐       │
│  │              Architecture Controller                 │       │
│  │  • Mutation operators      • Crossover operators    │       │
│  │  • Pruning strategies      • Compression methods   │       │
│  └──────────────────────────────────────────────────────┘       │
│         │                                                       │
│         ▼                                                       │
│  ┌──────────────────────────────────────────────────────┐       │
│  │              Optimization Engine                     │       │
│  │  • Evolutionary search    • Bayesian optimization  │       │
│  │  • Gradient-based NAS     • Reinforcement learning │       │
│  └──────────────────────────────────────────────────────┘       │
│                                                                 │
└─────────────────────────────────────────────────────────────────┘
```

## Search Strategies

### 1. Evolutionary Architecture Search

```python
class EvolutionaryNAS:
    """Evolutionary neural architecture search."""
    
    def __init__(self, population_size=50, mutation_rate=0.1):
        self.population_size = population_size
        self.mutation_rate = mutation_rate
        self.population = []
        self.fitness_history = []
    
    def initialize_population(self, search_space):
        """Initialize random architectures."""
        for _ in range(self.population_size):
            architecture = self.sample_architecture(search_space)
            self.population.append(architecture)
    
    def mutate(self, architecture):
        """Apply mutation operators."""
        mutated = architecture.copy()
        
        # Layer mutation
        if random.random() < self.mutation_rate:
            mutated = self.mutate_layer(mutated)
        
        # Connection mutation
        if random.random() < self.mutation_rate:
            mutated = self.mutate_connection(mutated)
        
        # Activation mutation
        if random.random() < self.mutation_rate:
            mutated = self.mutate_activation(mutated)
        
        return mutated
    
    def crossover(self, parent1, parent2):
        """Crossover two architectures."""
        child = Architecture()
        
        # Uniform crossover of layers
        for i in range(min(len(parent1.layers), len(parent2.layers))):
            if random.random() < 0.5:
                child.add_layer(parent1.layers[i])
            else:
                child.add_layer(parent2.layers[i])
        
        return child
    
    def evolve(self, fitness_scores):
        """Evolve population based on fitness."""
        # Selection
        selected = self.tournament_selection(fitness_scores)
        
        # Create new population
        new_population = []
        
        # Elitism
        elite_idx = np.argsort(fitness_scores)[-5:]
        for idx in elite_idx:
            new_population.append(self.population[idx])
        
        # Generate offspring
        while len(new_population) < self.population_size:
            parent1, parent2 = random.sample(selected, 2)
            child = self.crossover(parent1, parent2)
            child = self.mutate(child)
            new_population.append(child)
        
        self.population = new_population
```

### 2. Differentiable Architecture Search (DARTS)

```python
class DARTS:
    """Differentiable Architecture Search."""
    
    def __init__(self, num_cells=8, num_ops=14):
        self.num_cells = num_cells
        self.num_ops = num_ops
        self.arch_parameters = None
    
    def build_search_space(self):
        """Build differentiable search space."""
        # Architecture parameters (alpha)
        self.arch_parameters = nn.Parameter(
            torch.zeros(self.num_cells, self.num_ops)
        )
        
        # Operation set
        self.ops = [
            'none', 'skip_connect', 'avg_pool_3x3',
            'max_pool_3x3', 'sep_conv_3x3', 'sep_conv_5x5',
            'dil_conv_3x3', 'dil_conv_5x5', 'conv_7x1_1x7',
            'attention', 'se_module', 'mb_conv',
            'eff_conv', 'fused_conv'
        ]
    
    def get_architecture_weights(self):
        """Get softmax weights for architecture."""
        return F.softmax(self.arch_parameters, dim=-1)
    
    def forward(self, x, arch_weights):
        """Forward pass with architecture weights."""
        for cell_idx in range(self.num_cells):
            cell_output = 0
            for op_idx in range(self.num_ops):
                op = self.ops[op_idx]
                weight = arch_weights[cell_idx, op_idx]
                cell_output += weight * self.apply_op(op, x)
            x = cell_output
        return x
    
    def optimize_architecture(self, train_data, val_data, epochs=50):
        """Optimize architecture parameters."""
        optimizer = torch.optim.Adam([self.arch_parameters], lr=3e-4)
        
        for epoch in range(epochs):
            for train_batch, val_batch in zip(train_data, val_data):
                # Train weights
                self.train_weights(train_batch)
                
                # Train architecture
                arch_loss = self.compute_arch_loss(val_batch)
                optimizer.zero_grad()
                arch_loss.backward()
                optimizer.step()
        
        return self.get_final_architecture()
```

### 3. One-Shot NAS

```python
class OneShotNAS:
    """One-shot neural architecture search with supernet."""
    
    def __init__(self, supernet_config):
        self.supernet = self.build_supernet(supernet_config)
        self.sampler = UniformSampler()
    
    def build_supernet(self, config):
        """Build supernet containing all candidate architectures."""
        supernet = nn.ModuleDict()
        
        for stage in config['stages']:
            stage_modules = nn.ModuleList()
            for op in config['candidate_ops']:
                stage_modules.append(self.build_op(op))
            supernet[stage] = stage_modules
        
        return supernet
    
    def train_supernet(self, train_data, epochs=100):
        """Train supernet to evaluate all architectures."""
        optimizer = torch.optim.SGD(self.supernet.parameters(), lr=0.1)
        scheduler = CosineAnnealingLR(optimizer, epochs)
        
        for epoch in range(epochs):
            for batch in train_data:
                # Sample architecture
                arch = self.sampler.sample()
                
                # Forward pass with sampled architecture
                output = self.forward_with_arch(batch, arch)
                loss = F.cross_entropy(output, batch.labels)
                
                optimizer.zero_grad()
                loss.backward()
                optimizer.step()
            
            scheduler.step()
    
    def search_best_architecture(self, val_data, num_samples=1000):
        """Search for best architecture from trained supernet."""
        best_arch = None
        best_score = 0
        
        for _ in range(num_samples):
            arch = self.sampler.sample()
            score = self.evaluate_architecture(arch, val_data)
            
            if score > best_score:
                best_score = score
                best_arch = arch
        
        return best_arch, best_score
```

## Search Space Design

```python
class SearchSpace:
    """Define neural architecture search space."""
    
    def __init__(self):
        self.layers = self.define_layers()
        self.connections = self.define_connections()
        self.activations = self.define_activations()
        self.normalizations = self.define_normalizations()
    
    def define_layers(self):
        """Define available layer types."""
        return {
            'convolutional': [
                {'type': 'conv', 'kernel': 3, 'expand_ratio': [1, 2, 4, 6]},
                {'type': 'conv', 'kernel': 5, 'expand_ratio': [1, 2, 4, 6]},
                {'type': 'conv', 'kernel': 7, 'expand_ratio': [1, 2, 4, 6]},
                {'type': 'depthwise_conv', 'kernel': [3, 5, 7]},
                {'type': 'separable_conv', 'kernel': [3, 5, 7]},
                {'type': 'mb_conv', 'kernel': [3, 5, 7], 'expand_ratio': [1, 2, 4, 6]},
                {'type': 'efficient_conv', 'kernel': [3, 5, 7]},
            ],
            'attention': [
                {'type': 'self_attention', 'heads': [1, 2, 4, 8]},
                {'type': 'cross_attention', 'heads': [1, 2, 4, 8]},
                {'type': 'se_module', 'reduction': [4, 8, 16]},
                {'type': 'cbam', 'reduction': [4, 8, 16]},
                {'type': 'eca_module'},
            ],
            'pooling': [
                {'type': 'max_pool', 'kernel': [2, 3]},
                {'type': 'avg_pool', 'kernel': [2, 3]},
                {'type': 'adaptive_pool', 'output': [1, 2, 4]},
            ],
            'special': [
                {'type': 'identity'},
                {'type': 'zero'},
                {'type': 'dropout', 'rate': [0.1, 0.2, 0.3, 0.5]},
                {'type': 'ghost_module', 'ratio': [2, 3, 4]},
            ]
        }
    
    def define_connections(self):
        """Define connection patterns."""
        return {
            'skip': ['none', 'identity', 'projection'],
            'residual': ['pre_activation', 'post_activation', 'dense'],
            'multi_scale': ['fpn', 'unet', 'aspp', 'spp'],
            'attention': ['self', 'cross', 'axial', 'window'],
        }
```

## Model Compression

```python
class ModelCompressor:
    """Compress neural networks for deployment."""
    
    def __init__(self, model, target_size_mb=None, target_latency_ms=None):
        self.model = model
        self.target_size = target_size_mb
        self.target_latency = target_latency_ms
    
    def prune_model(self, sparsity=0.5, method='magnitude'):
        """Prune model weights."""
        if method == 'magnitude':
            self.magnitude_pruning(sparsity)
        elif method == 'structured':
            self.structured_pruning(sparsity)
        elif method == 'lottery_ticket':
            self.lottery_ticket_pruning(sparsity)
        
        return self.model
    
    def quantize_model(self, bits=8):
        """Quantize model weights."""
        quantized = nn.Module()
        
        for name, module in self.model.named_modules():
            if isinstance(module, nn.Conv2d):
                quantized.add_module(
                    name,
                    self.quantize_conv(module, bits)
                )
            elif isinstance(module, nn.Linear):
                quantized.add_module(
                    name,
                    self.quantize_linear(module, bits)
                )
        
        return quantized
    
    def knowledge_distillation(self, teacher, train_data, temperature=4.0):
        """Distill knowledge to smaller student model."""
        student = self.model
        optimizer = torch.optim.Adam(student.parameters())
        
        for batch in train_data:
            with torch.no_grad():
                teacher_output = teacher(batch)
            
            student_output = student(batch)
            
            # Knowledge distillation loss
            loss = self.kd_loss(
                student_output, teacher_output, 
                batch.labels, temperature
            )
            
            optimizer.zero_grad()
            loss.backward()
            optimizer.step()
        
        return student
```

## Performance Prediction

```python
class PerformancePredictor:
    """Predict model performance without training."""
    
    def __init__(self):
        self.encoder = ArchitectureEncoder()
        self.predictor = PredictorNetwork()
    
    def encode_architecture(self, architecture):
        """Encode architecture as feature vector."""
        features = {
            'num_params': self.count_params(architecture),
            'num_layers': len(architecture.layers),
            'flops': self.estimate_flops(architecture),
            'memory': self.estimate_memory(architecture),
            'depth': self.compute_depth(architecture),
            'width': self.compute_width(architecture),
            'gradient_flow': self.estimate_gradient_flow(architecture),
        }
        
        # Graph encoding
        graph_features = self.encoder.encode_graph(architecture)
        
        return torch.cat([
            torch.tensor(list(features.values())),
            graph_features
        ])
    
    def predict_accuracy(self, architecture, dataset_info):
        """Predict model accuracy."""
        features = self.encode_architecture(architecture)
        dataset_features = self.encode_dataset(dataset_info)
        
        combined = torch.cat([features, dataset_features])
        
        return self.predictor(combined)
    
    def predict_latency(self, architecture, hardware='gpu'):
        """Predict inference latency."""
        features = self.encode_architecture(architecture)
        hardware_features = self.encode_hardware(hardware)
        
        combined = torch.cat([features, hardware_features])
        
        return self.predictor.latency_head(combined)
```

## Process

1. **Define Search Space**: Identify architectural building blocks
2. **Select Strategy**: Choose NAS approach based on constraints
3. **Run Search**: Execute architecture optimization
4. **Evaluate Candidates**: Test top architectures on validation set
5. **Refine and Compress**: Optimize final architecture
6. **Deploy**: Export to target framework

## Output

- Optimal architecture specification
- Trained model weights
- Performance metrics (accuracy, latency, size)
- Architecture search history
- Recommended variants for different constraints
