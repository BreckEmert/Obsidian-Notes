Print the number of dogs/cats in the respective subfolders of `image_dir`
??
```python
print(
    len(list((image_dir / 'Cat').glob('*.jpg))),
    len(list((image_dir / 'Dog').glob('*.jpg)))
)
```

