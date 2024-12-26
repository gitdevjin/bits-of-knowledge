### React Scroll

```javascript
import { Link, Element } from "react-scroll";

export default function HomePage() {
  return (
    <div>
      <nav>
        <Link to="section1" smooth={true} duration={500}>
          Section 1
        </Link>
        <Link to="section2" smooth={true} duration={500}>
          Section 2
        </Link>
        <Link to="section3" smooth={true} duration={500}>
          Section 3
        </Link>
      </nav>

      <Element name="section1" className="section">
        Section 1
      </Element>
      <Element name="section2" className="section">
        Section 2
      </Element>
      <Element name="section3" className="section">
        Section 3
      </Element>
    </div>
  );
}
```

```javascript
## React Fullpage
import ReactFullpage from "@fullpage/react-fullpage";

export default function HomePage() {
  return (
    <ReactFullpage
      navigation
      scrollingSpeed={1000} // Scroll speed in milliseconds
      render={({ state, fullpageApi }) => (
        <ReactFullpage.Wrapper>
          {/* Section 1 */}
          <div className="section">
            <h1 className="text-white">Section 1</h1>
            <p>Content for Section 1</p>
          </div>
          {/* Section 2 */}
          <div className="section" style={{ backgroundColor: "#333" }}>
            <h1 className="text-white">Section 2</h1>
            <p>Content for Section 2</p>
          </div>
          {/* Section 3 */}
          <div className="section" style={{ backgroundColor: "#666" }}>
            <h1 className="text-white">Section 3</h1>
            <p>Content for Section 3</p>
          </div>
        </ReactFullpage.Wrapper>
      )}
    />
  );
}

// indigo color

```
