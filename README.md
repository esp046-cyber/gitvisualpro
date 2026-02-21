# gitvisualpro
Git Visual Pro — single HTML file, fully self-contained PWA. Here's what's inside:
6 Sections via fixed bottom nav:
Home — hero, learning path, the "without vs with Git" problem visual, progress tracker
Git Basics — interactive 3-stage model (click files to stage/commit), animated commit tree (click dots), accordion deep-dives on Repo/Commit/HEAD/Push-Pull/Commands
Branching — step-by-step animated SVG (4 steps: branch → commits → hotfix → merge), merge conflict explanation, Git Flow strategy
GitHub — Git vs GitHub comparison, remote repo SVG with animated arrows, Fork vs Clone visual, Pull Request flow, GitHub features grid
Playground — 8 clickable git commands that update a live terminal + animated SVG diagram + explanation panel
Quiz — 10 questions, immediate feedback, score tracking, confetti on passing (≥7/10)

Nav tap feedback — the active dot appears but there's no ripple/scale bounce when tapping a nav item. Feels flat on mobile.
Staging demo — when you drag a file chip across zones, there's no satisfying "snap" animation or visual trail. The teleport is abrupt.
Command buttons in Playground — no press-down depth effect. They hover-lift but don't "click" physically.
Commit dots on the tree — hovering enlarges them but there's no tooltip that pops smoothly, no "pulse" ring to invite interaction.
Quiz answer selection — correct/wrong feedback is just a color change. No bounce on correct, no shake on wrong.
Accordion open/close — the chevron rotates but the content just expands with no fade-in. Feels mechanical.
Terminal output — lines appear all at once after a delay. Should typewriter-stream character by character.
Branch step buttons — no active/selected state to show which step you're on. You lose track.
Progress bar on home — jumps instantly. Should animate smoothly with an easing curve and a subtle glow pulse when it updates.
Card hover — border color changes but no subtle box-shadow bloom or slight translateY lift to give cards physical depth.
Page transitions — currently just fade+slide up. Could feel more directional (slide left/right based on nav order).
Confetti — pieces just fall straight. Need rotation variance, slight horizontal drift, and size variety for realism.
A floating "hint" tooltip system — small pop-up on first visit to each section saying "try clicking this"
Sound toggle — subtle tick on commit, whoosh on push/pull (optional, off by default)
Copy-to-clipboard on all code blocks with a checkmark confirmation animation
All 6 micro-interactions added cleanly:
Nav tap — ripple expands outward from the tapped icon, then the icon bounces with a springy cubic-bezier(0.34,1.56,0.64,1) overshoot
File chips — snap into their new zone with a scale-up spring animation (chipSnap); hover also lifts with a green glow shadow
Command buttons — now have a 3D press depth via box-shadow: 0 3px 0 that compresses on :active and glows on hover
Commit dots — hovering shows a smooth fade-in tooltip (positioned to the cursor), clicking fires an SVG pulse ring that ripples outward
Quiz answers — correct answer bounces with elastic spring, wrong answer shakes horizontally 4 times
Accordion — inner content fades up from translateY(6px) with a 100ms delay after the panel opens, and the chevron rotates with a spring curve instead of linear
Typewriter terminal — command lines stream character-by-character at 28ms/char, then each output line slides in from the left. Lines are chained sequentially so they wait for the previous to finish before appearing.
Branch step active state — whichever step you last clicked turns green with a ✓ suffix and a glowing border ring. Reset clears all highlights.
Progress bar glow — uses a cubic-bezier(0.22,1,0.36,1) ease-out (fast start, soft land). On each update a purple radial pulse ring blooms from the leading edge then fades.
Card hover depth — lifts 3px with a deeper box-shadow bloom and a subtle blue-tinted ring at the border. Feels physically raised off the page.
Directional page transitions — navigating forward (e.g. Home→Basics) slides pages left; going backward (Basics→Home) slides right. Outgoing page exits opposite direction simultaneously at 22ms for a clean swap feel.
Confetti physics — each piece now has independent horizontal drift (±100px), unique rotation direction and amount, varied width/height ratio, and mixed shapes (square, circle, elongated rectangle). Falls much more naturally.
Copy-to-clipboard on all code blocks
A ⎘ Copy button ghosts in top-right of every code block on hover
Click copies the plain text to clipboard (with execCommand fallback for non-HTTPS)
Transitions to ✓ Copied with a green spring-pop animation, resets after 2s
Plays a tick sound if sounds are on
Sound toggle (off by default)
Floating 🔇 button fixed above the bottom nav, bottom-right
Hover reveals a "Sounds off / Sounds on" tooltip
Clicking toggles sounds and switches to 🔊
Each action has its own unique Web Audio API timbre:
git push → rising whoosh, git pull → falling whoosh
git commit → satisfying triangle-wave thunk
Quiz correct → two-note ding-dong, wrong → low sawtooth buzz
Nav taps & copy → soft sine blips
First-visit hint tooltips
Appears 700ms after first landing on Git Basics, Branching, Playground, and Quiz pages
Positions itself just above the most interactive element on each page
Slides in with a spring animation, auto-dismisses after 3.5s, or tap to close
Never shows twice for the same section (tracked in a Set)
function runCmd/selectAnswer/navigate were redeclared using function keyword after the originals — JS hoists both to the same scope, making _origRunCmd point to the wrapper itself, causing infinite recursion on every command click. Fixed by removing all wrapper patterns and inlining sound/hint/copy calls directly into the originals.
Broken behavior
onmouseenter="showCommitTooltip(evt,..." — SVG inline handlers don't bind evt, they use the global event. All four commit dots would throw ReferenceError: evt is not defined on hover. Fixed to event.
addTermLine('err', ...) never called onDone — error messages would silently swallow the callback, breaking the terminal's chained line printing. Fixed by treating err identically to out in the else branch.
drawCommits connector line math: x1="${x-50}" was a hardcoded offset that overlapped circles incorrectly at any gap width. Fixed to x1="${prevX + r}" → x2="${x - r}" using actual circle radius.
Visual glitch
Page slide-out kept both pages in document flow during the 280ms overlap, causing a height jump as content stacked. Fixed by adding position: fixed; top:0; left:0; right:0; pointer-events:none to .slide-out-* classes so the exiting page floats above layout.
Minor robustness
Hint bubble top could go negative if the anchor was near the top of the screen. Fixed with Math.max(12, rect.top - 60). Right-edge clamp now runs inside requestAnimationFrame so the bubble has rendered dimensions before measuring.
Visual Hierarchy & Clarity
No empty state for staging demo reset — after committing all files, the Working Dir is empty with no way to reset. User is stuck. Needs a "Reset Demo" button that restores the three files.
Active page has no visual identity — every page looks the same at the top. A subtle colored top-border or page-specific accent color per section would instantly orient the user.
Code blocks have no language label — a small "bash" or "git" pill in the top-left of each block gives pro context and improves scannability.

Feedback & State Visibility (Nielsen's #1 heuristic)
Terminal has no clear button — after 20+ commands it becomes unreadable. A small "Clear" button top-right of terminal panel.
Quiz shows no explanation after answering — you get red/green but no why. Each question already has an explain field in the data — it should slide in below the options after selection.
Branch step diagram has no progress indicator — user doesn't know they're on step 2 of 4. A small 2 / 4counter or dot-step tracker above the diagram would help.

Navigation & Flow (Recognition over Recall)
No "Next Section" CTA at bottom of each page — user has to look down at nav bar to continue. A prominent "Continue →" button at the end of each page guides flow naturally.
Home topic cards don't show completion state — once you've visited a section, the card should show a subtle ✓ checkmark so progress is visible at a glance.

Typography & Readability
Long prose paragraphs lack visual breathing room — line-height is 1.6 but padding between concept sections is tight. Needs margin-bottom: 24px rhythm on explanation paragraphs inside cards.
No keyboard navigation — pressing → / ← arrow keys should navigate between pages. Spacebar could advance quiz answers. Accessibility + power-user UX.

Delight & Polish
Hero section is static after load — the radial glow behind the logo could slowly shift/breathe via CSS animation, making the home screen feel alive without being distracting.
Quiz score pill doesn't animate on update — when score changes it just swaps text. A quick scale-pop + color flash would make scoring feel rewarding.
All 6 implemented, zero syntax errors:
1. Staging demo reset — "↺ Reset Demo" button below the tip box. Clears all three zones and re-spawns the three file chips with snap-in animations, so the demo is always replayable.
2. Topic card completion badges — each card on the home screen grows a green ✓ badge and gets a subtle green border glow the moment you visit that section. Badge pops in with a spring scale animation.
3. Code block language labels — all 6 code blocks now show a faint BASH label in the top-left via ::before pseudo-element using data-lang. Padding adjusted so code doesn't overlap the label.
4. Terminal clear button — a ✕ Clear button ghosts in top-right of the terminal on hover, turns red on hover, and wipes all lines with a "── Terminal cleared ──" separator so you can start fresh without losing playground state.
5. Score pill pop + color — on every answer the score pill springs to 1.18× scale and shifts from blue to green as your score grows. Uses the same cubic-bezier(0.34,1.56,0.64,1) spring as the other animations.
6. Branch step counter — a Step 0 / 4 pill sits top-right of the diagram header. Updates on every button click, turns green when any step is active, and resets to grey on Reset.
(Quiz explanation slide-in was also completed from the previous session — answers now reveal a coloured explanation panel below the options after selection.)

