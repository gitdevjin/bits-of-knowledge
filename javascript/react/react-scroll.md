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
import { useRef, useEffect, useState } from "react";
import Footer from "../../Footer/Footer";
import DotScroll from "./DotScroll";
import SectionOne from "./SectionOne";

export default function HomePage() {
  const outerRef = useRef();
  const [currentPage, setCurrentPage] = useState(1);
  const [isScrolling, setIsScrolling] = useState(false);

  const getNextPage = (scrollTop, deltaY) => {
    const pageHeight = window.innerHeight;

    if (deltaY > 0) {
      // Scrolling down
      if (scrollTop < pageHeight) return 2;
      if (scrollTop < pageHeight * 2) return 3;
      if (scrollTop < pageHeight * 3) return 4;
      return 4; // Already at the last page
    } else {
      // Scrolling up
      if (scrollTop < pageHeight) return 1; // Already at the first page
      if (scrollTop < pageHeight * 2) return 1;
      if (scrollTop < pageHeight * 3) return 2;
      return 3;
    }
  };

  useEffect(() => {
    const handleWheel = (e) => {
      e.preventDefault();
      if (isScrolling) return;

      const CUSHION_HEIGHT = 5;
      const pageHeight = window.innerHeight;
      const { deltaY } = e;
      const { scrollTop } = outerRef.current;

      const nextPage = getNextPage(scrollTop, deltaY);

      setIsScrolling(true);

      // Scroll logic
      const scrollPosition =
        (nextPage - 1) * pageHeight + CUSHION_HEIGHT * (nextPage - 1);

      outerRef.current.scrollTo({
        top: scrollPosition,
        left: 0,
        behavior: "smooth",
      });

      setCurrentPage(nextPage);

      setTimeout(() => {
        setIsScrolling(false);
      }, 1400);
    };

    const outerRefCurrent = outerRef.current;
    outerRefCurrent.addEventListener("wheel", handleWheel);

    return () => {
      outerRefCurrent.removeEventListener("wheel", handleWheel);
    };
  }, [isScrolling]);

  return (
    <main className="h-screen overflow-hidden m-0">
      <div ref={outerRef} className="h-screen overflow-y-auto fullpage-wrapper">
        <DotScroll currentPage={currentPage}></DotScroll>
        <div className="h-screen flex justify-center items-center ">
          <SectionOne></SectionOne>
        </div>
        <div className="w-[100%] h-[5px] "></div> {/* Divider */}
        <div className="h-screen flex justify-center items-center text-white text-3xl">
          2
        </div>
        <div className="w-[100%] h-[5px] "></div> {/* Divider */}
        <div className="h-screen flex justify-center items-center bg-purple-300">
          3
        </div>
        <div className="w-[100%] h-[5px] "></div> {/* Divider */}
        <div className="h-screen flex justify-center items-center bg-green-200">
          <Footer />
        </div>
      </div>
    </main>
  );
}

// indigo color


// indigo color

```
