# YAML: A Beginner's Guide

YAML (YAML Ain't Markup Language) is a lightweight, human-readable data serialization format. It's commonly used for configuration files, data exchange, and structured data representation. Unlike XML or JSON, YAML emphasizes readability and simplicity.

## Basic Rules of YAML

- File Extensions: YAML files typically have the .yaml or .yml extension.
- Case Sensitivity: YAML is case-sensitive. Be consistent with your key names.
- Indentation: YAML uses spaces (not tabs) for indentation.

### YAML Data Types

#### Scalars

Scalars represent single values. They can be strings, integers, or other simple types.

Example:

```yaml
max_retry: 100
database: users
```

In this snippet:

- max_retry is an integer.
- database is a string.

#### Mapping (Key-Value Pairs)

Mappings are dictionaries with key-value pairs.

Example:

```yaml
animals:
  name: cat
  age: 5
```

Here:

- animals is the key.
- The value is a dictionary with keys name and age.

#### Sequence (Lists)

Sequences are lists of items.

Example:

```yaml
employee:
  - age: 28
    name: Jack
  - age: 34
    name: Syd
```

In this case:

- employee is a list containing two dictionaries (each representing an employee).

### Use Cases for YAML

- Configuration Files: YAML is commonly used for configuring applications, services, and tools.
- Data Serialization: It's great for exchanging data between systems.
- Docker Compose: Docker Compose files use YAML to define multi-container applications.
- Kubernetes: Kubernetes configuration files (e.g., deployment, service) are written in YAML.

## Conclusion

YAML's simplicity and readability make it a popular choice for various tasks. Whether you're configuring a web server, defining a CI/CD pipeline, or describing a complex data structure, YAML has you covered.
