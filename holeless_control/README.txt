# FrozenLake 8x8 Q-learning experiment suite

Variant: holeless_control
Description: Control condition. Holes are replaced by frozen tiles.
Output directory: /Users/jifu/Desktop/rllib-rensyu/results_frozenlake8x8_qlearning_suite__20260422_125058_611498/holeless_control

Main outputs:
- tables/summary_per_seed.csv
- tables/aggregated_curves.csv
- tables/snapshot_distance_means.csv
- tables/final_aggregates.npz
- figures/*.png

Cases:
- det_sparse
- det_dense
- stoch_sparse
- stoch_dense

Run assumptions:
- fixed 8x8 map for this variant
- 100 seeds
- 30000 episodes per seed
- max_steps=200
- alpha=0.1
- gamma=0.99
- epsilon: 1.0 -> 0.05 over first 18000 episodes
- dense reward beta=0.2
- max_workers(requested)=8
- max_workers(actual)=8
