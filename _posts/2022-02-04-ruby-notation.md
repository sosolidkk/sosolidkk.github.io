---
layout: post
title: Ruby % Notation
date: 2022-02-04 20:20 -0300
tags: ruby tips
---
For a long time, my Rubocop always kept complaining about the list of symbols defined in my code and, I also didn't search for the reason behind it. It states that:
> Prefer %i to the literal array syntax when you need an array of symbols (and you don’t need to maintain Ruby 1.9 compatibility). Apply this rule only to arrays with two or more elements.

So after some searching, I've found that's pretty common and, generally, more readable for the developers to use this notation. So here goes a brief resume and examples about them:

```rb
# Yes, they are case sensitive
%q[ ] # Non-interpolated String (except for \\ \[ and \])
%Q[ ] # Interpolated String (default)
%r[ ] # Interpolated Regexp (flags can appear after the closing delimiter)
%i[ ] # Non-interpolated Array of symbols, separated by whitespace
%I[ ] # Interpolated Array of symbols, separated by whitespace
%w[ ] # Non-interpolated Array of words, separated by whitespace
%W[ ] # Interpolated Array of words, separated by whitespace
%x[ ] # Interpolated shell command
```

#### `%q`
```rb
%q{one\ntwo\n#{ 1 + 2 }}
# => "one\\ntwo\\n\#{ 1 + 2 }"
```
#### `%Q`
```rb
%Q{one\ntwo\n#{ 1 + 2 }}
# => "one\ntwo\n3"
```
#### `%r`
```rb
name = 'nemo'
%r/#{name}/i
# => /nemo/i
```
#### `%i`
```rb
%i[one two three] # after Ruby 2.0
# => [:one, :two, :three]
```
#### `%I`
```rb
value = 'searching'
%I[#{value} for nemo]
# => [:searching, :for, :nemo]
```
#### `%w`
```rb
%w[one two three]
# => ["one", "two", "three"]
```
#### `%W`
```rb
value = 'one'
%W[#{value} two three]
# => ["one", "two", "three"]
```
#### `%x`
```rb
%x{ruby --copyright}
# => "ruby - Copyright (C) 1993-2020 Yukihiro Matsumoto\n"
```
#### References
[Wikibooks - Ruby Programming](https://en.wikibooks.org/wiki/Ruby_Programming/Syntax/Literals#The_.25_Notation)

[Rubocop Style Guide](https://github.com/rubocop/ruby-style-guide#percent-w)