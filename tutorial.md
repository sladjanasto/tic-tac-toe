# 🎓 Build a Pure CSS Tic-Tac-Toe Game: A Step-by-Step SCSS Tutorial

Welcome to a deep dive into building a **fully functional Tic-Tac-Toe game using only CSS and SCSS** — no JavaScript involved. This tutorial guides you through every step of the process, with in-depth comments explaining how and why each piece works.

Whether you want to improve your SCSS skills, experiment with advanced selectors like `:has()`, or just try something unique, this project is both fun and educational.

---

## 🔧 The Setup: What We’ll Build

- A scalable Tic-Tac-Toe board (3x3 by default, easily adjustable)
- Two-player interactivity using `:checked` states
- Turn tracking and messaging without JavaScript
- Win and draw detection using smart selector logic
- Responsive and clean layout using SCSS mixins and maps

---

## 1. 🧱 Grid Generation Using SCSS

```scss
$num-of-fields: 9;
$width: 60;
$cell-size: calc($width / math.sqrt($num-of-fields)) - 2;

@function generate-grid($cols, $rows) {
  $map: ();
  $index: 1;
  $gap: calc(
    ($width - sqrt($num-of-fields) * $cell-size) / (sqrt($num-of-fields) - 1)
  );

  @for $y from 0 through ($rows - 1) {
    @for $x from 0 through ($cols - 1) {
      $coord-x: $x * ($cell-size + $gap);
      $coord-y: $y * ($cell-size + $gap);
      $map: map.merge(
        $map,
        (
          $index: (
            $coord-x,
            $coord-y,
          ),
        )
      );
      $index: $index + 1;
    }
  }
  @return $map;
}

$grid-map: generate-grid(sqrt($num-of-fields), sqrt($num-of-fields));
```

### 🧠 Explanation

This function dynamically calculates the `(x, y)` coordinates for each field based on its grid position. These coordinates will later be used to absolutely position the game elements like labels and fields.

---

## 2. 🏆 Winning Logic

```scss
@function generate-winning-combinations($size) {
  $map: ();

  @for $row from 0 to $size {
    $combo: ();
    @for $col from 1 through $size {
      $index: $row * $size + $col;
      $combo: list.append($combo, $index);
    }
    $map: map.merge(
      $map,
      (
        "row-#{$row + 1}": $combo,
      )
    );
  }

  @for $col from 1 through $size {
    $combo: ();
    @for $row from 0 to $size {
      $index: $col + $row * $size;
      $combo: list.append($combo, $index);
    }
    $map: map.merge(
      $map,
      (
        "col-#{$col}": $combo,
      )
    );
  }

  $diag1: ();
  @for $i from 0 to $size {
    $index: 1 + $i * ($size + 1);
    $diag1: list.append($diag1, $index);
  }
  $map: map.merge(
    $map,
    (
      "diag-1": $diag1,
    )
  );

  $diag2: ();
  @for $i from 1 through $size {
    $index: $i * $size - ($i - 1);
    $diag2: list.append($diag2, $index);
  }
  $map: map.merge(
    $map,
    (
      "diag-2": $diag2,
    )
  );

  @return $map;
}

$winning-combinations: generate-winning-combinations(sqrt($num-of-fields));
```

### 🧠 Explanation

This function builds a map of all possible winning lineups on the grid: horizontal rows, vertical columns, and the two diagonals. Each combination is a list of cell indices. These will be used to detect and style the winning states later.

---

## 3. 🎯 Turn System with CSS Variables

```scss
@mixin turn-system($max-turns: $num-of-fields) {
  $player-x: ();
  $player-o: ();
  $player-o: list.append(
    $player-o,
    ".tic-tac-toe_board:has(input:checked)",
    comma
  );

  $chain: "input:checked";
  @for $turn from 2 through $max-turns {
    $chain: "#{$chain} ~ input:checked";
    $selector: ".tic-tac-toe_board:has(#{$chain})";

    @if ($turn % 2 == 0) {
      $player-x: list.append($player-x, $selector, comma);
    } @else {
      $player-o: list.append($player-o, $selector, comma);
    }
  }

  #{$player-o} {
    --player: "Player O plays next";
  }
  #{$player-x} {
    --player: "Player X plays next";
  }

  $allCels: ();
  $input: "input:checked ~";
  @for $i from 2 through $max-turns {
    $allCels: list.append($allCels, $input, space);
    @if $i == $max-turns {
      $allCels: list.append($allCels, string.slice($input, 1, -3), space);
    }
  }
  .tic-tac-toe_board:has(#{$allCels}) {
    --player: "It's a draw.";
  }
}
```

### 🧠 Explanation

Using chained `input:checked` selectors, this mixin determines the turn order. It sets a CSS variable `--player` that updates the UI to show who plays next, or whether the game is a draw when all checkboxes are selected.

---

## 4. 🧼 Dynamic Styling with Mixins

```scss
@mixin flex-center($direction: row, $justify: center, $align: center) {
  display: flex;
  flex-direction: $direction;
  justify-content: $justify;
  align-items: $align;
}

@mixin size($width, $aspect) {
  width: $width;
  aspect-ratio: $aspect;
}

@mixin set-symbol($i, $symbol, $size, $color) {
  & ~ #field-#{$i}::after {
    content: if($symbol == "x", var(--signX), var(--signO));
    font-size: $size;
    color: $color;
    font-weight: bold;
  }
}
```

### 🧠 Explanation

We use reusable mixins to keep our code DRY. `flex-center` handles alignment, `size` keeps aspect ratios consistent, and `set-symbol` dynamically injects player symbols based on state.

---

## 5. 🧪 Full Game Demo

Check out the full demo and play the game: 👉 [Live Demo on CodePen](#)

Feel free to modify the board size, colors, or layout!

---

## ✅ Conclusion

This tutorial shows how powerful CSS and SCSS can be when you approach styling as logic. With the help of `:checked`, `:has()`, maps, and mixins, you can simulate real state-driven behavior entirely within your stylesheet.

No JavaScript. No DOM manipulation. Just pure styling magic.

Let’s keep pushing the boundaries of what CSS can do 🚀
