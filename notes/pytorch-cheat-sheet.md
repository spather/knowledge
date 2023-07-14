# PyTorch Cheat Sheet

## Number of Model Parameters

```python
trainable_params = [p for p in m.parameters() if p.requires_grad]
nparams = sum([np.prod(p.size()) for p in trainable_params])
nparams
```
