---
title: "about.ex"
date: 2018-07-01T21:42:43-04:00
---
```elixir
defmodule AboutMe do
  defstruct name: "Mafinar Khan",
            tagline: "Pythonista. Djangonaut. Alchemist",
            date_of_birth: ~D[1982-12-07],
            location: %{
              city: "Toronto",
              province: "ON",
              country: "Canada"
            },
            work: %{
              company_name: "Tread Inc",
              designation: "Senior Software Engineer",
              email: "mafinar@tread.io",
              tags: ~w/golang graphql java android python django apollo react/
            },
            skills: %{
              programming_languages: [
                python: %{since: 2001, love: 9 / 10, score: 8 / 10},
                elixir: %{since: 2016, love: 10 / 10, score: 7 / 10},
                clojure: %{since: 2011, love: 9 / 10, score: 5 / 10},
                javascript: %{since: 2002, love: 1 / 10, score: 6 / 10},
                golang: %{since: 2017, love: 5 / 10, score: 6 / 10},
                java: %{since: 2001, love: 7 / 10, score: 7 / 10}
              ],
              frameworks: %{
                django: %{since: 2009, love: 9 / 10, score: 8 / 10},
                phoenix: %{since: 2018, love: 9 / 10, score: 5 / 10},
                react: %{since: 2015, love: 3 / 10, score: 6 / 10},
                mobx: %{since: 2015, love: 5 / 10, score: 6 / 10},
                vue: %{since: 2016, love: 1 / 10, score: 6 / 10}
              },
              databases: %{
                postgres: %{since: 2002, love: 10 / 10, score: 6 / 10}
              },
              others: %{
                functional_programming: %{
                  since: 2003,
                  love: 10 / 10,
                  score: 7 / 10,
                  continue?: true
                },
                gis: %{since: 2009, love: 7 / 10, score: 5 / 10}
              }
            },
            interests: ~w/gis graph_theory lambda_calculus nlp/,
            random_facts: %{
              editors: ~w/vscode vi/,
              os: ~w/*nix android/,
              envies: ~w/haskell elm c/,
              movie: ~w/mystery thriller horror/,
              music: "*/**",
              superheroes: ~w/batman lobo/,
              food: ~w/curry vietnamese middle-eastern/,
              languages_can_speak: ~w/bengali english hindi urdu esperanto/,
              languages_can_write: ~w/bengali english/
            }

  # --- TO BE CONTINUED
end
```