# tslib

this is a patched version of microsoft/tslib to make Jest work in ESM-enabled projects that include tslib somehow.

The problem is that Jest will parse the tslib.js file and it can't figure out the factory pattern and decides that there are no exports. See https://github.com/microsoft/tslib/issues/173

The workaround is to manually set all the exports.

To use this, convince you package manager to use https://github.com/StratoKit/tslib.git as the version for tslib.

We use pnpm and we have a .pnpmfile.cjs like:

```js
// eslint-disable-next-line max-params
const replace = (pkg, context, name, version, regex) => {
	const dep = pkg.dependencies[name]
	if (dep && (!regex || regex.test(dep))) {
		pkg.dependencies[name] = version
		context.log(`replacing ${pkg.name}'s ${name} dep ${dep} with ${version}`)
	}
}
const readPackage = (pkg, context) => {
	if (!pkg.dependencies) return pkg
	replace(pkg, context, 'tslib', 'https://github.com/StratoKit/tslib.git')
	return pkg
}

module.exports = {hooks: {readPackage}}
```
