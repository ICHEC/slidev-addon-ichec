# Slidev Addon: ICHEC

This is the official [Slidev](https://sli.dev/) addon containing custom layouts, components, and styling for High-Performance Computing (HPC) presentations. 

By installing this addon, you get access to all ICHEC corporate components without needing to clutter your presentation repository with `.vue` files.

## 📦 Installation

Ensure you have your Slidev presentation repository initialized, then install this addon using `pnpm`:

```bash
pnpm add -D slidev-addon-ichec
```

## 🚀 Usage

To activate the addon, simply add it to the addons array in the frontmatter of your slides.md file:

```yaml
---
theme: default
addons:
  - slidev-addon-ichec
---

# My Presentation
```

Slidev will automatically detect and import all the components. You can now use them directly in your markdown!

## 🛠️ Available Components

Here is a quick reference for the most commonly used components:

1. Banners (`Banner`)

Draw attention to warnings, tips, or success messages.

```html
<Banner type="warning" title="Important Note">
  Ensure you are connected to the cluster before running this command.
</Banner>

<AnimatedBanner title="Quiz Time" speed="30">
  What is the theoretical peak performance?
</AnimatedBanner>
```

2. Gradient Text (`Gt`)

Highlight specific text or inline math with ICHEC gradients.

```html
Welcome to the <Gt from="blue" to="green">Future of $x^2$</Gt>.

<Gt block from="blue" to="yellow">

$$
E = mc^2
$$

</Gt>
```

3. Code Windows

Wrap standard markdown code fences in a beautiful macOS-style window.

``````html
<CodeWindow title="submit.sh" width="full">

```bash
#!/bin/bash
#SBATCH -N 1
#SBATCH -p compute
srun ./my_program
```

</CodeWindow>
``````

For the complete visual documentation and component showcase, please see the ICHEC's [template showcase](https://ichec.github.io/slidev-lectures/).

## 📄 License

This project is licensed under the [Apache License 2.0](./LICENSE).