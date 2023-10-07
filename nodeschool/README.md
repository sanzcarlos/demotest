# 👍 Curso de NodeJS
Vamos a realizar el curso de NodeJs de [nodeschool.io](https://nodeschool.io).

Ese curso lo vamos a seguir a través del video de [Carlos Azaustre - Primeros pasos con NodeJS](https://www.youtube.com/watch?v=zBdojhZ2_Qg)

# HUSKY & LINT-STAGED
HUSKY funciona con los GIT HOOKS, por lo que tenemos que trabajar con un repositorio de GIT.

Los Git Hooks son scripts que se ejecutan en momentos determinados, como antes de un commit, push, etc...

Por ejemplo el script de pre-commit nos permite que antes de finalizar un commit, se ejecute un script que corra el Linter y nos diga si hay errores o no y asegurarnos que el código que se sube al repositorio sigue las reglas de estilo.

Lint-Staged nos permite ejecutar scripts en los archivos que se van a subir al repositorio, es decir, solo en los archivos que hemos modificado, no en todos los archivos del proyecto. Se entiende que el resto de archivos que no hemos tocado ya están bien y no necesitan ser revisados.

```shell
npm install -D husky lint-staged
```

# Configuración
Creamos un fichero `.lintstagedrc` en la raíz de nuestro proyecto, con el siguiente contenido:

```shell
{
  "*.{js,jsx,ts,tsx}": ["eslint --fix", "git add"]
}
```

Aqui le estamos diciendo que para los ficheros con extensión js, jsx, ts y tsx (Javascript y Typescript), ejecute el comando eslint --fix y después añada los cambios al commit.

Ahora en nuestro `package.json` añadimos un script que podemos llamar prepare. Puedes añadirlo en la sección scripts o ejecutar el siguiente comando:

```shell
npm pkg set scripts.prepare="husky install"
```

Esto nos permite configurar Husky de una forma más rápida corriendo:

```shell
npm run prepare
```

Esto instala los git hooks dentro de la carpeta .husky que se acaba de crear.

## Añadir el pre-commit hook
Ahora vamos a añadir el pre-commit hook, para que antes de hacer un commit, se ejecute el script de Lint-Staged. Esto lo hacemos con el siguiente comando:

```shell

npx husky add .husky/pre-commit "npx lint-staged"
```

Esto crea el fichero .husky/pre-commit con el siguiente contenido:

```shell
#!/usr/bin/sh
. "$(dirname "$0")/_/husky.sh"

npx lint-staged
```

# Configurar Linter
Tenemos el hook pero no tenemos un linter configurado. Solucionemos eso. Ejecutamos el siguiente comando para que se instale y configure ESlint de una forma rápida:

```shell
npx eslint --init

❯ npx eslint --init

Need to install the following packages:
eslint@8.51.0
Ok to proceed? (y)
You can also run this command directly using 'npm init @eslint/config'.
✔ How would you like to use ESLint? · style
✔ What type of modules does your project use? · esm
✔ Which framework does your project use? · none
✔ Does your project use TypeScript? · No / Yes
✔ Where does your code run? · browser
✔ How would you like to define a style for your project? · guide
✔ Which style guide do you want to follow? · standard-with-typescript
✔ What format do you want your config file to be in? · JSON
Checking peerDependencies of eslint-config-standard-with-typescript@latest
Local ESLint installation not found.
The config that you've selected requires the following dependencies:

eslint-config-standard-with-typescript@latest @typescript-eslint/eslint-plugin@^6.4.0 eslint@^8.0.1 eslint-plugin-import@^2.25.2 eslint-plugin-n@^15.0.0 || ^16.0.0  eslint-plugin-promise@^6.0.0 typescript@*
✔ Would you like to install them now? · No / Yes
✔ Which package manager do you want to use? · npm
Installing eslint-config-standard-with-typescript@latest, @typescript-eslint/eslint-plugin@^6.4.0, eslint@^8.0.1, eslint-plugin-import@^2.25.2, eslint-plugin-n@^15.0.0 || ^16.0.0 , eslint-plugin-promise@^6.0.0, typescript@*

added 202 packages, and audited 257 packages in 8m

119 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
Successfully created .eslintrc.json file in /home/csanz/git/demotest/nodeschool
```

# Añadir el pre-push hook
Ahora vamos a añadir el pre-push hook, para que antes de hacer un push, se ejecuten los scripts que determinemos, como por ejemplo que se lancen los Tests y sólo si pasan, el código se suba finalmente al repositorio.Esto lo hacemos con el siguiente comando:


```shell
npx husky add .husky/pre-push "npm run test"
```

Para que puedas probar este ejemplo, vamos a crear un fichero y un pequeño test, además de la configuración de ESLint necesaria para que no te de errores:

Primero de todo, instalaré Jest como runner de tests:

```shell
npm install -D jest

❯ npm install -D jest

added 294 packages, and audited 551 packages in 2m

138 packages are looking for funding
  run `npm fund` for details

found 0 vulnerabilities
```

Seguidamente, para no complicarnos mucho con este ejemplo, en un fichero `.eslintignore` vamos a añadir la carpeta de `__tests__` y ya que estamos también node_modules por curarnos en salud.

```shell
//.eslintignore
node_modules
__test__
```
Tendremos un fichero de ejemplo, index.js que simplemente exporta una función que devuelve la suma de dos valores:

```shell
// index.js
const sum = (a, b) => a + b;

module.exports = { sum };
```

Y en la carpeta __test__ tendremos un fichero index.test.js que importará la función sum y hará un test para comprobar que funciona correctamente:

```shell
// index.test.js
const { sum } = require("./index");

test("adds 1 + 2 to equal 3", () => {
  expect(sum(1, 2)).toBe(3);
});
```

Puedes jugar con esto para ver como el test falla y no te deja realizar el push.

# Enlaces
  - [Cómo configurar Husky y Lint-Staged en un proyecto](https://carlosazaustre.es/husky-lintstaged)
