[![PyPI](https://img.shields.io/pypi/v/basilisp-pprint.svg?style=flat-square)](https://pypi.org/project/basilisp-pprint/) [![CI](https://github.com/ikappaki/basilisp-pprint/actions/workflows/tests-run.yml/badge.svg)](https://github.com/ikappaki/basilisp-pprint/actions/workflows/tests-run.yml)

# A Port of the Clojure Pretty Print Library to Basilisp

[Basilisp](https://github.com/basilisp-lang/basilisp) is a Python-based Lisp implementation that offers broad compatibility with Clojure. For more details, check out the [documentation](https://basilisp.readthedocs.io/en/latest/index.html).

## Overview
`basilisp-pprint` is a source code port of the `clojure.pprint` core library to [Basilisp](https://github.com/basilisp-lang/basilisp).

The `clojure.pprint` library provides pretty formatting capabilities for common Clojure data structures such as lists, maps, sets, vectors, as well as Clojure code. The library allows for customizable printing through various options and parameters.

`basilisp-pprint` provides these capabilities to Basilisp as an external library, allowing you to use it while the native `basilisp.pprint` support (note the dot in the name) is [still in development](https://basilisp.readthedocs.io/en/latest/api/pprint.html). This includes enhanced API features, such as the [cl-format](https://clojure.github.io/clojure/clojure.pprint-api.html#clojure.pprint/cl-format) function.

## Installation

To install `basilisp-pprint`, run:

```shell
pip install basilisp-pprint
```

## Usage

To use the library, require it as `basilisp-pprint.pprint` in your Basilisp code.

For general usage instructions, refer to the [`clojure.pprint documentation`](https://clojure.github.io/clojure/clojure.pprint-api.html).

## Examples

### Pretty Printing a Nested Map

```clojure
;; https://clojuredocs.org/clojure.pprint/pprint#example-542692d0c026201cdc326ec5
(require '[basilisp-pprint.pprint :as p])

(def big-map (zipmap
              [:a :b :c :d :e]
              (repeat
               (zipmap [:a :b :c :d :e]
                       (take 5 (range))))))
;; => #'user/big-map

big-map
;; => {:e {:e 4 :a 0 :c 2 :d 3 :b 1} :a {:e 4 :a 0 :c 2 :d 3 :b 1} :c {:e 4 :a 0 :c 2 :d 3 :b 1} :d {:e 4 :a 0 :c 2 :d 3 :b 1} :b {:e 4 :a 0 :c 2 :d 3 :b 1}}

(p/pprint big-map)
;; {:e {:e 4, :a 0, :c 2, :d 3, :b 1},
;;  :a {:e 4, :a 0, :c 2, :d 3, :b 1},
;;  :c {:e 4, :a 0, :c 2, :d 3, :b 1},
;;  :d {:e 4, :a 0, :c 2, :d 3, :b 1},
;;  :b {:e 4, :a 0, :c 2, :d 3, :b 1}}
```

### Pretty Printing Clojure Code

```clojure
;; https://clojuredocs.org/clojure.pprint/pprint#example-5b950e6ce4b00ac801ed9e8a
;;
;; get Clojure snippets to print nicely
(require '[basilisp-pprint.pprint :as p]
         'basilisp.edn)

(def lost-formatting
  "(defn op [sel] (condp = sel :plus + :minus - :mult *
   :div / :rem rem :quot quot :mod mod))")
;; => #'user/lost-formatting

(p/with-pprint-dispatch
  p/code-dispatch
  (p/pprint
   (basilisp.edn/read-string lost-formatting)))
;; (defn op [sel]
;;   (condp = sel
;;     :plus +
;;     :minus -
;;     :mult *
;;     :div /
;;     :rem rem
;;     :quot quot
;;     :mod mod))
```

### Pretty Printing with Metadata

```clojure
;; https://clojuredocs.org/clojure.pprint/pprint#example-6318f958e4b0b1e3652d765f
;;
;; pprint with metadata
(require '[basilisp-pprint.pprint :as p])

(binding [*print-meta* true]
  (p/pprint ^{:hello :world} {}))
;; ^{:hello :world} {}
```

### Pretty Printing Tables

```clojure
;; https://clojuredocs.org/clojure.pprint/print-table#example-542692d6c026201cdc3270f1
;;
;; If there are keys not in the first row, and/or you want to specify only
;; some, or in a particular order, give the desired keys as the first arg.
(require '[basilisp-pprint.pprint :as p])

(p/print-table [:b :a] [{:a 1 :b 2 :c 3} {:b 5 :a 7 :c "dog"}])
;; | :b | :a |
;; |----+----|
;; |  2 |  1 |
;; |  5 |  7 |
```

### Using `cl-format`

```clojure
;; cl-format example
(require '[basilisp-pprint.pprint :as p])

(let [name "Alice"
      born 1999
      hobbies ["reading" "hiking" "hacking"]]
  (-> (p/cl-format nil "Name: ~A~%Born: ~@R~%Hobbies: ~{~A~^, ~}~%"
                      name
                      born
                      hobbies)
      print))
;; Name: Alice
;; Born: MCMXCIX
;; Hobbies: reading, hiking, hacking
```
## Development and Testing

The approach taken for this port was to make as few changes as possible to the original `clojure.pprint` source code in order to maintain consistency. While the current implementations is feature-complete and works quite well, it has not yet been fully optimized for Python.

To run the test suite, use the following command:

```shell
basilsp test
```

## License

This project is licensed under the Eclipse Public License 1.0. See the [LICENSE](LICENSE) file for details.

## Acknowledgments

@chrisrink10, for developing [Basilisp](https://github.com/basilisp-lang/basilisp) and ensuring API compatibility with Clojure, enabling smooth ports of Clojure source code to Python.

```
;;; pprint.clj -- Pretty printer and Common Lisp compatible format function (cl-format) for Clojure

;   Copyright (c) Rich Hickey. All rights reserved.
;   The use and distribution terms for this software are covered by the
;   Eclipse Public License 1.0 (http://opensource.org/licenses/eclipse-1.0.php)
;   which can be found in the file epl-v10.html at the root of this distribution.
;   By using this software in any fashion, you are agreeing to be bound by
;   the terms of this license.
;   You must not remove this notice, or any other, from this software.

;; Author: Tom Faulhaber
;; April 3, 2009
;;
;; Porting to Basilisp initiated from Clojure,
;; based on the code available at
;; https://github.com/clojure/clojure/blob/08a2d9bdd013143a87e50fa82e9740e2ab4ee2c2/src/clj/clojure/pprint.clj
```
