## Sectioned RecyclerView

Sectioned RecyclerView allows you to easily split a RecyclerView into sections with headers.

<img src="https://raw.githubusercontent.com/afollestad/sectioned-recyclerview/master/art/showcase1.png" width="300" />

---

## Gradle Dependency

[ ![jCenter](https://api.bintray.com/packages/drummer-aidan/maven/sectioned-recyclerview/images/download.svg) ](https://bintray.com/drummer-aidan/maven/sectioned-recyclerview/_latestVersion)
[![Build Status](https://travis-ci.org/afollestad/sectioned-recyclerview.svg)](https://travis-ci.org/afollestad/sectioned-recyclerview)
[![License](https://img.shields.io/badge/license-Apache%202-4EB1BA.svg?style=flat-square)](https://www.apache.org/licenses/LICENSE-2.0.html)

The Gradle dependency is available via [jCenter](https://bintray.com/drummer-aidan/maven/sectioned-recyclerview/view).
jCenter is the default Maven repository used by Android Studio.

### Dependency

Add this to your module's `build.gradle` file:

```gradle
dependencies {
    // ... other dependencies
    compile 'com.afollestad:sectioned-recyclerview:0.4.1'
}
```

## Adapter

Here's a basic example:

```java
public class MainAdapter extends SectionedRecyclerViewAdapter<MainAdapter.MainVH> {

  @Override
  public int getSectionCount() {
    return 20; // number of sections.
  }

  @Override
  public int getItemCount(int section) {
    return 8; // number of items in section (section index is parameter).
  }

  @Override
  public void onBindHeaderViewHolder(MainVH holder, int section, boolean expanded) {
    // Setup header view.
  }

  @Override
  public void onBindViewHolder(MainVH holder, int section, int relativePosition, int absolutePosition) {
    // Setup non-header view.
    // 'section' is section index.
    // 'relativePosition' is index in this section.
    // 'absolutePosition' is index out of all non-header items.
    // See sample project for a visual of how these indices work.
  }

  @Override
  public MainVH onCreateViewHolder(ViewGroup parent, int viewType) {
    // Change inflated layout based on 'header'.
    int layoutRes = viewType == VIEW_TYPE_HEADER ? R.layout.header : R.layout.normal;
    View v = LayoutInflater.from(parent.getContext())
      .inflate(layoutRes, parent, false);
    return new MainVH(v);
  }

  public static class MainVH 
    extends SectionedViewHolder
    implements View.OnClickListener {

    public MainVH(View itemView) {
      super(itemView);
      // Setup view holder.
      // You'd want some views to be optional, e.g. for header vs. normal.
      itemView.setOnClickListener(this);
    }
    
    @Override
    public void onClick(View view) {
      // SectionedViewHolder exposes methods such as:
      boolean isHeader = isHeader();
      ItemCoord position = getRelativePosition();
      int section = position.section();
      int relativePos = position.relativePos();
    }
  }
}
```

---

## Expanding and Collapsing Sections

Expanding and collapsing sections is easy:

```java
MainAdapter adapter = // ...
adapter.expandSection(int);
adapter.collapseSection(int);
adapter.toggleSectionExpanded(int);
boolean isExpanded = adapter.isSectionExpanded(int);
```

---

## Hiding Empty Sections

You can tell the adapter to hide sections which have no items.

```java
MainAdapter adapter = // ...
adapter.shouldShowHeadersForEmptySections(false);
```

## Notifying Adapter of Section Changes

Like `notifyDataSetChanged()`, `notifyItemInserted(int)`, `notifyItemRemoved(int)` (etc.), the 
sectioned adapter provides a method called `notifySectionChanged(int)`. It notifies the adapter 
that every item in that section has changed, rather than every item in the whole adapter. 
`onBindViewHolder` will be called again for each item in the section.

You should **not** use this method when a section has been added or removed. You should always use 
`notifyDataSetChanged()` for that to avoid data corruption in the adapter.

---

## Layout Manager

If you're using a `LinearLayoutManager`, you're all set. If you're using a `GridLayoutManager`,
you need to tell the adapter:

```java
GridLayoutManager manager = // ...
adapter.setLayoutManager(manager);
```

This is vital to getting headers to span all columns.
