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
