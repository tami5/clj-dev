# clj-dev
> Data-driven clojure development environment.

The objective of `clj-dev` is to minimize the complexity of managing clojure
development environments and keeping `user.clj` clean. Templating and
bootstrapping is bad idea for maintaining consistent development environments.

## Installation 
Leiningen/Boot

```clojure 
[org.clojars.kkharji/clj-dev "0.1.1"]
```

Clojure CLI/deps.edn
```clojure
org.clojars.kkharji/clj-dev {:mvn/version "0.1.1"}
```

## Status

- [x] Hot-reload set of dirs and reload changed files
- [x] Support [integrant] and [duct framework] state management.
- [x] Easy configuration language.
- [ ] Replace hawk with [beholder].
- [ ] Wrap [nrepl] and [cider-nrepl] and expose start function.
- [ ] Wrap [viso/pretty] for pretty print exceptions.
- [ ] Support running and watching tests, maybe wrap [kaocha].
- [ ] Support [mount].

[duct framework]: https://github.com/weavejester/integrant
[integrant]: https://github.com/weavejester/integrant
[beholder]: https://github.com/nextjournal/beholder
[cider-nrepl]: https://github.com/clojure-emacs/cider-nrepl
[viso/pretty]: https://github.com/AvisoNovate/pretty
[kaocha]: https://github.com/lambdaisland/kaocha
[mount]: https://github.com/tolitius/mount
[nrepl]: https://github.com/nrepl/nrepl

## Usage

### 1. Create `user.clj`

```clojure
(ns user
 (:require [clj-dev.core :as d]
           [potemkin :as p]))

(p/import-vars ;; expose api functions in user namespace. (e.g. user/start )
 [clj-dev.core start pause resume stop restart watch system config])
 ;; or aliases: go, halt, reset, reset-all

;; Initialized with default options
(d/init)

(comment
  ;; Map of current state. Most likely would be only integrant state.
  system
  ;; Set refresh-dirs, start state, load local.clj if it exists.
  ;; Pass true as first argument to start watch process.
  ;; Alias: go
  (start)
  ;; Stop the development environment, clear state and namespace tracker.
  ;; Pass false as first argument to keep current watch job running if any.
  ;; Alias: halt!, halt
  (stop)
  ;; Restart development environment by running (stop) and start.
  ;; Pass true as first argument to restart watch job too.
  ;; Alias: reset-all
  (restart)
  ;; Pause state, "only effective with state management".
  ;; alias: suspend
  (pause)
  ;; Resume state after pausing, "only effective with state management".
  (resume)
  ;; Refresh development environment by running (pause) and (start).
  ;; alias: reset
  (refrsh)
  ;; Start watching `:watch-paths` for changes. If development environment
  ;; isn't started yet, then run (start).
  (watch)
  ;; Stop watching `:watch-paths` for changes.
  (watch :stop))
```

### 2. Create alias in `deps.edn`
```clojure
{:aliases
 :repl {:main-opts   ["-m" "nrepl.cmdline" "--middleware"
                      "[cider.nrepl/cider-middleware]" "--interactive"]
        :extra-paths ["test" "dev/src"]
        :extra-deps  {cider/cider-nrepl {:mvn/version "0.27.2"}
                      org.clojars.kkharji     {:mvn/version "0.1.0"}}}}
```


### 3. Use
```
user => (user/start) ;; or (start)
user => (user/stop) ;; or (stop)
```

## Default Options

```clojure
{;; By default only watch and namespace reload and refresh works
 ;; Paths to target for refresh & tests
 :paths ["src" "test" "dev" "resources" "dev/src" "dev/resources"]
 ;; Whether to auto-start i.e. when calling clj-dev/init, call clj-dev/start
 :start-on-init? false ;;

 ;; Directories to watch for changes. default to :paths
 :watch-paths nil ;; vector
 ;; File patterns to trigger reload on.
 :watch-pattern #"[^.].*(\.clj|\.edn)$"
 ;; time stamp format, set to nil if you don't want have timestamp.
 :watch-timestamp "[hh:mm:ss]"

 ;; Integrant file configuration path within :paths.
 :integrant-file-path nil ;; string
 ;; integrant profiles.
 :integrant-profiles nil ;; vector
 ;; Whether duct framework should be considered.
 :integrant-with-duct? nil} ;; boolean
```

## License

Copyright © 2021 kkharji

Distributed under the Eclipse Public License version 1.0.
