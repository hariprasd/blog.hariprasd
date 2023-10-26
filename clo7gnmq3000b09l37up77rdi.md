---
title: "Creating a hover triggered text weight animation in Next Js / React - with source code"
datePublished: Thu Oct 26 2023 17:31:01 GMT+0000 (Coordinated Universal Time)
cuid: clo7gnmq3000b09l37up77rdi
slug: creating-a-hover-triggered-text-weight-animation-in-next-js-react-with-source-code
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1698340853434/e464b478-c0ad-4dbd-9b19-b2dea019237b.gif
tags: typography, reactjs, nextjs, framer-motion, interaction-design

---

We will be creating a NextJs component that receives a string as a prop & animates it, Code below 👇

As a bonus, We will be using **Framer motion & Tailwind CSS** to trigger a letter-by-letter **slide-in animation**

### Component Structure

1. **Letters Reference**: The `lettersRef` is created using the `useRef` hook to store references to the individual letter elements in the text.
    
2. **Mouse Interaction Effect**: An `useEffect` hook is used to set up a mouse movement interaction effect. It listens for mouse movements to change the font properties of each letter based on their proximity to the mouse cursor.
    
    * The `handleMouseMove` function calculates the proximity of each letter to the mouse and modifies font properties such as `fontWeight` and `fontVariationSettings` accordingly.
        
    * The effect adds a `mousemove` event listener to trigger the interaction and remove it when the component unmounts.
        
3. **Rendering Animated Text**: The component renders the animated text.
    
    * It splits the input `text` into individual letters and maps over them.
        
    * Each letter is wrapped in a `motion.h1` element, making use of Framer Motion for animation.
        
    * The animation includes initial opacity and y-position, and a delay is applied to each letter for a staggered entrance effect.
        
    * The `exit` transition is defined to control how the letters disappear.
        
    * A reference to the DOM element for each letter is stored in the `lettersRef` using the `ref` attribute.
        
4. **Export**: The `AnimatedText` component is exported for use in other parts of the application.
    

```javascript
    "use client";  // NextJs will need this, React users feel free to  remove this
    
    import React, { useRef, useEffect } from "react";
    import { motion, AnimatePresence} from "framer-motion";
    
    const AnimatedText = ({ text }) => {
        const lettersRef = useRef([]);  // Create a reference for the letters
    
        useEffect(() => {
            // This effect sets up mouse movement interaction for the letters.
            const handleMouseMove = (e) => {
                // Handle mouse movement to change font properties based on proximity to the mouse.
                lettersRef.current.forEach((letter, index) => {
                    if (letter) {
                        const rect = letter.getBoundingClientRect();
                        const dx = e.clientX - (rect.left + rect.width / 2);
                        const dy = e.clientY - (rect.top + rect.height / 2);
                        const distance = Math.sqrt(dx * dx + dy * dy);
                        const maxDistance = 150;  // Maximum distance for interaction (feel free adjust this).
                        const proximity = Math.max(0, maxDistance - distance) / maxDistance;
                        
                        // Modify the font properties based on proximity to the mouse.
                        letter.style.fontWeight = `${300 + proximity * 1000}`;
                        letter.style.fontVariationSettings = `"wdth" ${20 * proximity + 100}`;
                    }
                });
            };
    
            // Add a mousemove event listener to trigger the interaction.
            window.addEventListener("mousemove", handleMouseMove);
    
            // Clean up the event listener when the component unmounts.
            return () => {
                window.removeEventListener("mousemove", handleMouseMove);
            };
        }, []);
    
        return (
            <AnimatePresence mode='wait'>
                {text.split("").map((letter, index) => (
                    <motion.h1
                        key={index}
                        initial={{ opacity: 0, y: 30 }}
                        animate={{
                            opacity: 1,
                            y: 0,
                            transition: { duration: 0.8, delay: index * 0.1 },
                        }}
                        exit={{ opacity: 0 }}
                        transition={{ duration: 0.3 }}
                        className="text-4xl inline-block anim-fst lg:text-8xl whitespace-nowrap"
                        ref={(el) => (lettersRef.current[index] = el)}
                    >
                        {letter} 
                    </motion.h1>
                ))}
            </div>
        );
    };
    
    export default AnimatedText;
 
```

Thanks for visiting this blog, Feel free to reach me for creating Modern Interactive Web development projects , Visit my portfolio - [Hariprasad](https://hariprasd.me) and my [Design agency](https://devignx.tech)