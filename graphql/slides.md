---
layout: cover
---

# Let's Talk About GraphQL

FCIP June 2021 - Matt Kunze

---
layout: cover
---

# About me

- Software Architect at Nutrien Digital
- 20+ years experience - Perl (?!?), Windows, JS and React
- Primary focus on web development platforms lately
- <mdi-email class="inline-block" /> matt.kunze@gmail.com | <mdi-github class="inline-block" /> https://github.com/MattKunze

---

# <mdi-graphql class="inline-block text-pink-500" /> GraphQL?

* Declarative data query and manipulation API for web applications
* Created by Facebook and released publicly in 2015
* https://graphql.org - Public foundation

<div class="slidev-layout three-columns w-full h-full grid grid-cols-3 gap-2">
  <div>Describe your data

```graphql
type Project {
  name: String
  tagline: String
  contributors: [User]
}
```
  </div>
  <div>Ask for what you want

```graphql
{
  project(name: "GraphQL") {
    tagline
  }
}
```
  </div>
  <div>Get predictable results

```json
{
  project(name: "GraphQL") {
    tagline
  }
}
```
  </div>
</div>

---

# Benefits

- Demand-oriented, client-driven APIs
  - Limit overfetching
  - Different aspects of data graph are
- Static type system and expressive definitions
  - Ensures valid requests and provides expectations about responses
  - Built-in documentation
- Inherent CQRS structure
  - Top level <kbd>Query</kbd> and <kbd>Mutation</kbd> types
  - Prefer operation oriented actions (<kbd>updateProjectName</kbd>) over
    implementation oriented actions (<kbd>patchProject</kbd>)

---

# Compared to REST

- REST endpoints are typically one-to-one with application features
  - New features require new endpoints
  - Encourages misuse of endpoints to support similar features
- REST typically exposes CRUD semantics
- GraphQL presents explicit type hierarchy across all requests
- Easier to evolve frontend and backend independently with GraphQL

---

# Queries

```graphql
query ProjectDetails($name: String!) {
  project(name: $name) {
    name
    tagline
    contributors {
      name
      email
    }
  }
}
```

- You only get what you ask for

---

# Using Queries (with apollo-client)

```tsx
const QUERY = gql`query ProjectDetails($name: String) { ... })`

export default function ProjectDetails({ name }) {
  const { data, loading, errors } = useQuery(QUERY, {
    variables: { name }
  })

  if (loading) return <Spinner />
  if (errors) return <ErrorMessage errors="errors" />

  const { project } = data
  return (
    <>
      <div>Project Name: {project.name}</div>
      <div classname="subtitle">{project.tagline}</div>
      {project.contributors && project.contributors.map(contributor => (
        <ContributorInfo {...contributor} />
      ))}
    </>
  )
}
```

---

# Mutations

```tsx
const MUTATION = gql`
  mutation UpdateProjectName($currentName: String!, $newName: String!) {
    updateProjectName(currentName: $currentName, newName: $newName) {
      name
      tagline
    }
  }`

export default function EditProjectName({ name }) {
  const [newName, setNewName] = useState(name)
  const [updateName] = useMutation(MUTATION)

  const handleInputChange = (e) => setNewName(e.target.value)
  const handleButtonClick = () =>
    updateName({ variables: { currentName: name, newName }})

  return (
    <input type="text" value={newName} onChange={handleInputChange} />
    <button onClick={handleButtonClick}>Update Name</button>
  )
}
```

- Cache invalidation and updates depends on schema definition and the specific client library

---

# Subscriptions

```tsx
const COMMENTS_SUBSCRIPTION = gql`
  subscription OnCommentAdded($postID: ID!) {
    commentAdded(postID: $postID) {
      id
      content
    }
  }
`

export default function LatestComment({ postID }) {
  const { data: { commentAdded }, loading } = useSubscription(
    COMMENTS_SUBSCRIPTION,
    { variables: { postID } }
  )
  return <h4>New comment: {!loading && commentAdded.content}</h4>
}
```

- Requires websocket connections so server and additional setup

---

# Resolvers

---

# Strategies

- Facade over existing services
  - Build APIs and schema for the application, then iterate backend changes as needed
- Service backed by database/document store
- Managed services
- Federation

---

# Challenges

- Does not follow HTTP semantics
  - All requests are <kbd>POST</kbd> by default
  - Response is typically <kbd>200</kbd> even if there are errors

<Tweet id="1384074981840097289" scale=".65" />

---

# Challenges

> There’s nothing that will prevent a GraphQL service from being versioned like
> any other REST API. That said, GraphQL avoids versioning by design.
> [(link)](https://graphql.org/faq/#how-does-versioning-work-in-graphql)

- Change management
  - Additive modifications
  - <kbd>@deprecated</kbd>

- Machine-to-machine use

---

# Tools

- Apollo
- Relay
- GraphQL Tools
- Hasura

> Note that GraphQL is a public specification, so frontend and backend tools are largely interoperable

---

# Scaling

- Apollo Federation - https://www.apollographql.com/docs/federation/

<svg id="mermaid-1623492000192" width="100%" xmlns="http://www.w3.org/2000/svg" xmlnsxlink="http://www.w3.org/1999/xlink" height="233" style="max-width:628.609375px" viewBox="0 0 628.609375 233"><style>#mermaid-1623492000192{font-family:"trebuchet ms",verdana,arial,sans-serif;font-size:16px;fill:#333;}#mermaid-1623492000192 .error-icon{fill:#552222;}#mermaid-1623492000192 .error-text{fill:#552222;stroke:#552222;}#mermaid-1623492000192 .edge-thickness-normal{stroke-width:2px;}#mermaid-1623492000192 .edge-thickness-thick{stroke-width:3.5px;}#mermaid-1623492000192 .edge-pattern-solid{stroke-dasharray:0;}#mermaid-1623492000192 .edge-pattern-dashed{stroke-dasharray:3;}#mermaid-1623492000192 .edge-pattern-dotted{stroke-dasharray:2;}#mermaid-1623492000192 .marker{fill:#333333;stroke:#333333;}#mermaid-1623492000192 .marker.cross{stroke:#333333;}#mermaid-1623492000192 svg{font-family:"trebuchet ms",verdana,arial,sans-serif;font-size:16px;}#mermaid-1623492000192 .label{font-family:"trebuchet ms",verdana,arial,sans-serif;color:#333;}#mermaid-1623492000192 .cluster-label text{fill:#333;}#mermaid-1623492000192 .cluster-label span{color:#333;}#mermaid-1623492000192 .label text,#mermaid-1623492000192 span{fill:#333;color:#333;}#mermaid-1623492000192 .node rect,#mermaid-1623492000192 .node circle,#mermaid-1623492000192 .node ellipse,#mermaid-1623492000192 .node polygon,#mermaid-1623492000192 .node path{fill:#ECECFF;stroke:#9370DB;stroke-width:1px;}#mermaid-1623492000192 .node .label{text-align:center;}#mermaid-1623492000192 .node.clickable{cursor:pointer;}#mermaid-1623492000192 .arrowheadPath{fill:#333333;}#mermaid-1623492000192 .edgePath .path{stroke:#333333;stroke-width:1.5px;}#mermaid-1623492000192 .flowchart-link{stroke:#333333;fill:none;}#mermaid-1623492000192 .edgeLabel{background-color:#e8e8e8;text-align:center;}#mermaid-1623492000192 .edgeLabel rect{opacity:0.5;background-color:#e8e8e8;fill:#e8e8e8;}#mermaid-1623492000192 .cluster rect{fill:#ffffde;stroke:#aaaa33;stroke-width:1px;}#mermaid-1623492000192 .cluster text{fill:#333;}#mermaid-1623492000192 .cluster span{color:#333;}#mermaid-1623492000192 div.mermaidTooltip{position:absolute;text-align:center;max-width:200px;padding:2px;font-family:"trebuchet ms",verdana,arial,sans-serif;font-size:12px;background:hsl(80,100%,96.2745098039%);border:1px solid #aaaa33;border-radius:2px;pointer-events:none;z-index:100;}#mermaid-1623492000192 .node rect,#mermaid-1623492000192 .node circle,#mermaid-1623492000192 .node polygon,#mermaid-1623492000192 .node path{stroke-width:2px;stroke:#3f20ba;fill:#F4F6F8;}#mermaid-1623492000192 .node.secondary rect,#mermaid-1623492000192 .node.secondary circle,#mermaid-1623492000192 .node.secondary polygon,#mermaid-1623492000192 .node.tertiary rect,#mermaid-1623492000192 .node.tertiary circle,#mermaid-1623492000192 .node.tertiary polygon{fill:white;}#mermaid-1623492000192 .node.secondary rect,#mermaid-1623492000192 .node.secondary circle,#mermaid-1623492000192 .node.secondary polygon{stroke:#f25cc1;}#mermaid-1623492000192 .cluster rect,#mermaid-1623492000192 .node.tertiary rect,#mermaid-1623492000192 .node.tertiary circle,#mermaid-1623492000192 .node.tertiary polygon{stroke:#41d9d3;}#mermaid-1623492000192 .cluster rect{fill:none;stroke-width:2px;}#mermaid-1623492000192 .label,#mermaid-1623492000192 .edgeLabel{background-color:white;line-height:1.3;}#mermaid-1623492000192 .edgeLabel rect{background:none;fill:none;}#mermaid-1623492000192 .messageText,#mermaid-1623492000192 .noteText,#mermaid-1623492000192 .loopText{font-size:12px;stroke:none;}#mermaid-1623492000192 g rect,#mermaid-1623492000192 polygon.labelBox{stroke-width:2px;}#mermaid-1623492000192 g rect.actor{stroke:#26a29d;fill:white;}#mermaid-1623492000192 g rect.note{stroke:#f25cc1;fill:white;}#mermaid-1623492000192 g line.loopLine,#mermaid-1623492000192 polygon.labelBox{stroke:#3f20ba;fill:white;}#mermaid-1623492000192:root{--mermaid-font-family:"trebuchet ms",verdana,arial,sans-serif;}#mermaid-1623492000192 flowchart{fill:apa;}</style><g><g class="output"><g class="clusters"></g><g class="edgePaths"><g class="edgePath LS-webapp LE-gateway" id="L-webapp-gateway" style="opacity:1"><path class="path" d="M242.5078125,186L242.5078125,161L280.78028441011236,136" marker-end="url(#arrowhead15)" style="fill:none;stroke-width:2px;stroke-dasharray:3"></path><defs><marker id="arrowhead15" viewBox="0 0 10 10" refX="9" refY="5" markerUnits="strokeWidth" markerWidth="8" markerHeight="6" orient="auto"><path d="M 0 0 L 0 0 L 0 0 z" style="fill:#333"></path></marker></defs></g><g class="edgePath LS-iosapp LE-gateway" id="L-iosapp-gateway" style="opacity:1"><path class="path" d="M378.7578125,186L378.7578125,161L340.48534058988764,136" marker-end="url(#arrowhead16)" style="fill:none;stroke-width:2px;stroke-dasharray:3"></path><defs><marker id="arrowhead16" viewBox="0 0 10 10" refX="9" refY="5" markerUnits="strokeWidth" markerWidth="8" markerHeight="6" orient="auto"><path d="M 0 0 L 0 0 L 0 0 z" style="fill:#333"></path></marker></defs></g><g class="edgePath LS-gateway LE-serviceA" id="L-gateway-serviceA" style="opacity:1"><path class="path" d="M260.4140625,106.24010043041606L92.8203125,72L92.8203125,47" marker-end="url(#arrowhead17)" style="fill:none"></path><defs><marker id="arrowhead17" viewBox="0 0 10 10" refX="9" refY="5" markerUnits="strokeWidth" markerWidth="8" markerHeight="6" orient="auto"><path d="M 0 0 L 0 0 L 0 0 z" style="fill:#333"></path></marker></defs></g><g class="edgePath LS-gateway LE-serviceB" id="L-gateway-serviceB" style="opacity:1"><path class="path" d="M310.6328125,97L310.6328125,72L310.6328125,47" marker-end="url(#arrowhead18)" style="fill:none"></path><defs><marker id="arrowhead18" viewBox="0 0 10 10" refX="9" refY="5" markerUnits="strokeWidth" markerWidth="8" markerHeight="6" orient="auto"><path d="M 0 0 L 0 0 L 0 0 z" style="fill:#333"></path></marker></defs></g><g class="edgePath LS-gateway LE-serviceC" id="L-gateway-serviceC" style="opacity:1"><path class="path" d="M360.8515625,106.41019400352734L532.1171875,72L532.1171875,47" marker-end="url(#arrowhead19)" style="fill:none"></path><defs><marker id="arrowhead19" viewBox="0 0 10 10" refX="9" refY="5" markerUnits="strokeWidth" markerWidth="8" markerHeight="6" orient="auto"><path d="M 0 0 L 0 0 L 0 0 z" style="fill:#333"></path></marker></defs></g></g><g class="edgeLabels"><g class="edgeLabel" transform="" style="opacity:1"><g transform="translate(0,0)" class="label"><rect rx="0" ry="0" width="0" height="0"></rect><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;white-space:nowrap"><span id="L-L-webapp-gateway" class="edgeLabel L-LS-webapp' L-LE-gateway"></span></div></foreignObject></g></g><g class="edgeLabel" transform="" style="opacity:1"><g transform="translate(0,0)" class="label"><rect rx="0" ry="0" width="0" height="0"></rect><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;white-space:nowrap"><span id="L-L-iosapp-gateway" class="edgeLabel L-LS-iosapp' L-LE-gateway"></span></div></foreignObject></g></g><g class="edgeLabel" transform="" style="opacity:1"><g transform="translate(0,0)" class="label"><rect rx="0" ry="0" width="0" height="0"></rect><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;white-space:nowrap"><span id="L-L-gateway-serviceA" class="edgeLabel L-LS-gateway' L-LE-serviceA"></span></div></foreignObject></g></g><g class="edgeLabel" transform="" style="opacity:1"><g transform="translate(0,0)" class="label"><rect rx="0" ry="0" width="0" height="0"></rect><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;white-space:nowrap"><span id="L-L-gateway-serviceB" class="edgeLabel L-LS-gateway' L-LE-serviceB"></span></div></foreignObject></g></g><g class="edgeLabel" transform="" style="opacity:1"><g transform="translate(0,0)" class="label"><rect rx="0" ry="0" width="0" height="0"></rect><foreignObject width="0" height="0"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;white-space:nowrap"><span id="L-L-gateway-serviceC" class="edgeLabel L-LS-gateway' L-LE-serviceC"></span></div></foreignObject></g></g></g><g class="nodes"><g class="node tertiary" id="flowchart-webapp-13" transform="translate(242.5078125,205.5)" style="opacity:1"><rect rx="5" ry="5" x="-45.046875" y="-19.5" width="90.09375" height="39" class="label-container"></rect><g class="label" transform="translate(0,0)"><g transform="translate(-35.046875,-9.5)"><foreignObject width="70.09375" height="19"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;white-space:nowrap">Web app</div></foreignObject></g></g></g><g class="node tertiary" id="flowchart-iosapp-14" transform="translate(378.7578125,205.5)" style="opacity:1"><rect rx="5" ry="5" x="-41.203125" y="-19.5" width="82.40625" height="39" class="label-container"></rect><g class="label" transform="translate(0,0)"><g transform="translate(-31.203125,-9.5)"><foreignObject width="62.40625" height="19"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;white-space:nowrap">iOS app</div></foreignObject></g></g></g><g class="node default" id="flowchart-gateway-15" transform="translate(310.6328125,116.5)" style="opacity:1"><rect rx="19.5" ry="19.5" x="-50.21875" y="-19.5" width="100.4375" height="39" class="label-container"></rect><g class="label" transform="translate(0,0)"><g transform="translate(-35.34375,-9.5)"><foreignObject width="70.6875" height="19"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;white-space:nowrap">Gateway</div></foreignObject></g></g></g><g class="node default" id="flowchart-serviceA-16" transform="translate(92.8203125,27.5)" style="opacity:1"><rect rx="0" ry="0" x="-84.8203125" y="-19.5" width="169.640625" height="39" class="label-container"></rect><g class="label" transform="translate(0,0)"><g transform="translate(-74.8203125,-9.5)"><foreignObject width="149.640625" height="19"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;white-space:nowrap">Products subgraph</div></foreignObject></g></g></g><g class="node default" id="flowchart-serviceB-17" transform="translate(310.6328125,27.5)" style="opacity:1"><rect rx="0" ry="0" x="-82.9921875" y="-19.5" width="165.984375" height="39" class="label-container"></rect><g class="label" transform="translate(0,0)"><g transform="translate(-72.9921875,-9.5)"><foreignObject width="145.984375" height="19"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;white-space:nowrap">Reviews subgraph</div></foreignObject></g></g></g><g class="node default" id="flowchart-serviceC-18" transform="translate(532.1171875,27.5)" style="opacity:1"><rect rx="0" ry="0" x="-88.4921875" y="-19.5" width="176.984375" height="39" class="label-container"></rect><g class="label" transform="translate(0,0)"><g transform="translate(-78.4921875,-9.5)"><foreignObject width="156.984375" height="19"><div xmlns="http://www.w3.org/1999/xhtml" style="display:inline-block;white-space:nowrap">Inventory subgraph</div></foreignObject></g></g></g></g></g></g></svg>

- GraphQL Tools Schema Stitching - https://www.graphql-tools.com/docs/schema-stitching/

---

# Resources
